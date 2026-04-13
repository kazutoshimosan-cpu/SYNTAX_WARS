# Web Audio API ブラウザ挙動の違いと対策

SYNTAX WARS プロジェクトで実際に遭遇した問題と対応をまとめた資料。

---

## 1. AudioContext の自動停止（Autoplay Policy）

### 概要
ブラウザはユーザー操作なしに音声を再生させないため、`new AudioContext()` した直後は `state = 'suspended'` になる。

### ブラウザ別の挙動

| ブラウザ | 挙動 |
|---------|------|
| Chrome 66+ | ページ読み込み時に自動で `suspended`。ユーザー操作後に `resume()` が必要 |
| Safari (macOS/iOS) | 同上。iOS はさらに厳しく、`resume()` が user gesture のコールスタック内でないと拒否される場合がある |
| Firefox | Chrome に近い挙動 |

### 対策
`onclick` や `ontouchstart` などのユーザー操作ハンドラ内で同期的に `audioCtx.resume()` を呼ぶ。

```javascript
element.addEventListener('click', () => {
  audioCtx.resume(); // 同期的に呼ぶことが重要
});
```

---

## 2. decodeAudioData を suspended 状態で呼ぶと失敗する

### 症状
- 初回ロード時にBGMが鳴らない
- リロード後（キャッシュあり）は鳴る
- SEは鳴る（小さいファイルは成功しやすい）

### 原因
`AudioContext` が `suspended` の状態で `decodeAudioData()` を呼ぶと、大きなファイル（BGMなど数MB）はデコードが無音で失敗することがある。
小さいファイル（SE など数十KB）は成功しやすい。
2回目以降はブラウザキャッシュで高速に読み込まれるため成功する。

### 対策
ページ読み込み時は `fetch` して `ArrayBuffer` を保存するだけにとどめ、デコード（`decodeAudioData`）はユーザー操作後（`resume()` 完了後）に行う。

```javascript
// プリロード時：保存だけ
fetch(url).then(r => r.arrayBuffer()).then(ab => rawBuffers[url] = ab);

// ユーザー操作後：デコード
audioCtx.resume().then(() => {
  decodeAudioData(rawBuffers[url], (buf) => { buffers[url] = buf; });
});
```

---

## 3. iOS Safari で audioCtx.resume() の Promise がハングする

### 症状
- iOS Safari で `resume().then(callback)` の `callback` が呼ばれない
- 音声だけでなく、音声に依存している処理も止まる

### 原因
iOS Safari では `audioCtx.resume()` が返す Promise が特定の状況で resolve されないことがある（iOS の Audio Session 管理との競合と考えられる）。

### 対策
ゲームのUIアニメーションなど音声と無関係な処理を `resume().then()` の中に入れない。

```javascript
// NG：アニメーションが音声待ちになる
audioCtx.resume().then(() => {
  startAnimation(); // iOSでハングすると動かない
  playBGM();
});

// OK：アニメーションは即座に、音声は非同期で独立
startAnimation(); // すぐ動く
audioCtx.resume().then(() => {
  playBGM();
}).catch(() => {});
```

---

## 4. decodeAudioData のコールバックと Promise の二重呼び出し

### 概要
`decodeAudioData` は旧API（コールバック）と新API（Promise）の両方をサポートしているブラウザがある。両方同時に使うと `onSuccess` が2回呼ばれる可能性がある。

### 対策
`settled` フラグで1回のみ処理されるよう制御する。

```javascript
function decodeAndStore(path, arrayBuffer) {
  return new Promise(resolve => {
    let settled = false;
    const ok  = buf => { if (!settled) { settled = true; buffers[path] = buf; resolve(); } };
    const err = ()  => { if (!settled) { settled = true; resolve(); } };
    const result = audioCtx.decodeAudioData(arrayBuffer.slice(0), ok, err);
    if (result?.then) result.then(ok).catch(err); // Promise も対応
  });
}
```

---

## 5. 非同期 playBGM の競合（古い呼び出しが後から再生される）

### 症状
- BGMが2つ同時に再生される
- 間違ったBGMがループし続ける
- 前のBGMが途中で突然止まる

### 原因
`playBGM` がデコード待ちの `await` 中に別の `playBGM` が呼ばれると、後から完了した古い呼び出しが再生を上書きする。

```
playBGM('opening')  →  await decode  →  [後から再生開始] ← 問題
playBGM('title')    →  await decode  →  [先に再生開始]
```

### 対策
世代番号（generation counter）を使い、古い呼び出しを `await` 後にキャンセルする。

```javascript
let bgmGen = 0;

async function playBGM(key) {
  stopBGM();
  const myGen = ++bgmGen; // 呼び出し時に世代を確保

  await audioCtx.resume();
  if (myGen !== bgmGen) return; // 新しい呼び出しが来たのでキャンセル

  await decodeOne(path);
  if (myGen !== bgmGen) return; // デコード中に新しい呼び出しが来たのでキャンセル

  bgmSource = play(path);
}
```

---

## 6. ブラウザ別まとめ

| 問題 | Chrome (PC) | Safari (PC) | iOS Safari | 対策 |
|------|:-----------:|:-----------:|:----------:|------|
| suspended状態でのデコード失敗 | △（大ファイルで発生） | △ | ✗（ほぼ失敗） | ユーザー操作後にデコード |
| resume() Promise ハング | - | - | △（発生することがある） | アニメーションを音声と分離 |
| autoplay 制限 | ✗ | ✗ | ✗ | 全ブラウザ共通でユーザー操作後に再生 |
| decodeAudioData 二重呼び出し | △ | △ | - | settled フラグで制御 |

凡例：✗ = 問題あり、△ = 条件付きで問題あり、- = 問題なし

---

## 実装パターン（SYNTAX WARS で採用）

```
[ページ読み込み]
  └─ fetch で全音声ファイルを ArrayBuffer として保存（デコードしない）

[最初のユーザー操作（タップ/クリック）]
  ├─ UIアニメーション即座に開始（音声待ちにしない）
  └─ audioCtx.resume().then(() => {
       decodeAll();        // 全ファイルをバックグラウンドでデコード
       playBGM('opening'); // デコード完了後に再生（世代番号で競合防止）
     })

[BGM切り替え]
  └─ playBGM(key)
       ├─ 世代番号インクリメント
       ├─ await resume() → 世代チェック
       ├─ await decode  → 世代チェック
       └─ 再生開始
```
