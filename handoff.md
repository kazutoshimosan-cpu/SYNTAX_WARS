# SYNTAX WARS v6.2 — 引き継ぎドキュメント

最終更新: 2026-04-11

---

## 1. 全体的な進捗

### 完成している機能

| 機能 | 状態 |
|------|------|
| ターン制戦闘（同時選択・同時解決） | ✅ 完成 |
| 6キャラクター実装（NEXUS-7/SHADOW/NOVA QUEEN/TITAN/LYRA-X/BLAZE） | ✅ 完成 |
| キャラ固有スキル（TITAN オーバーヒート、BLAZE 誘爆 等） | ✅ 完成 |
| オーディエンスゲージ（CROWD ON FIRE） | ✅ 完成 |
| ジャストガード | ✅ 完成 |
| 難易度選択（EASY/NORMAL/HARD） | ✅ 完成 |
| 5連戦モード | ✅ 完成 |
| オートプレイ（CPU vs CPU）/ テストデータ収集 | ✅ 完成 |
| Firebase Firestore ロギング（match_results / action_logs） | ✅ 完成 |
| オンライン対戦（ホスト/ゲスト） | ✅ 動作確認済み |
| プリロード＆ローディング画面（Now Loading...） | ✅ 完成 |
| 音声システム（Web Audio API） | ✅ 実装済み・要動作確認 |
| フィードバックフォーム（Google Forms リンク） | ✅ 完成 |
| Netlify デプロイ | ✅ 公開中 |

### 最新バランス調整（全てプロトタイプ反映済み）

- 強攻撃ヒット率: 65% → 78%
- カウンター返しヒット率: 必中 → 80%（外れ時は自分もかすりダメージ）
- カウンター自分被ダメ: SPEED差に応じて 0〜30%
- SHADOW 通常ダメージ: -20% → -10%、SPEED 65固定（自然回復なし）
- 加速コスト: 30% → 15%、加速上昇量: +20〜30
- HP 40%以下でゲージ増加量 ×1.5
- BLAZE 誘爆: 自爆ダメージの50%が相手にも入る
- TITAN オーバーヒート: FAST状態で通常攻撃がガード不可・攻撃-10%

---

## 2. 主要ファイルと役割

### `syntax_wars_v62.html`（唯一のファイル）

1ファイル完結（HTML + CSS + JS）。約3900行。

#### 主要モジュール構成

```
AssetLoader         画像を fetch → blob URL にキャッシュ（現状は画像のみ使用）

AudioManager        Web Audio API ベースの音声エンジン（IIFE）
  ├─ decodeAndStore(path, ab)  ArrayBuffer → AudioBuffer（iOS コールバック対応版）
  ├─ getAllAudioPaths()         プリロード対象パス一覧を返す
  ├─ playSE(key, vol)          効果音再生（createBufferSource 毎回生成）
  ├─ playBGM(key)              BGM 再生（async・resume 待ち）
  ├─ stopBGM()                 BGM 停止
  └─ unlock()                  audioCtx.resume() を呼ぶ（Promise 返す）

DOMContentLoaded    プリローダー
  ├─ 画像 6枚を Image オブジェクトでブラウザキャッシュへ
  ├─ 音声 51ファイルを fetch → decodeAndStore
  ├─ 進捗バー表示（NOW LOADING... X%）
  └─ 完了後にローディングオーバーレイを非表示

setTimeout(20秒)    フォールバック：何があってもオーバーレイを強制消去

openingClick()      初回タップで audioCtx.resume() → BGM + タイピング演出
```

#### 音声ファイルのパス構造（変更済み）

```
assets/sound/
├── system,bgm/          ← フォルダ名にカンマあり（注意）
│   ├── system/          ready.mp3, opening.mp3, systemdecision.mp3, characterdecision.mp3
│   └── BGM/             title.mp3, characterchoosing.mp3, result.mp3, battle1-3.mp3
└── battle/
    ├── common/          crowd on fire.mp3, counter.mp3, justguard.mp3 ... 等13ファイル
    ├── NEXUS/           nanobureido.mp3, EMPhadou.mp3, purazumakanon.mp3, ryousibiimu.mp3
    ├── SHADOW/          kunainage.mp3, kusarigama.mp3, kagegiri.mp3, kagebunsin.mp3, zetsuei.mp3
    ├── NOVA/            energyball.mp3, jigensaki.mp3, jyuuryokuba.mp3, hosikuzuhou.mp3
    ├── TITAN/           tekken.mp3, jisinkudaki.mp3, gigantpuresu.mp3, superdenjihou.mp3
    ├── LYRA-X/          sanekihunsya.mp3, henitsume.mp3, hobakusyokusyu.mp3, saibousaisei.mp3, jidoukaihuku.mp3
    └── BLAZE/           fireball.mp3, kaenhousya.mp3, magmastrike.mp3, kibakujyunnbi.mp3, bakuretsuken.mp3
```

> **注意**: 旧パス（`システム、BGM/`、`戦闘効果音/` 等の日本語）は全て削除済み。
> fetch 時は `encodeURI(url)` を使用（カンマをエンコードしない）。

#### オンライン対戦の重要な設計メモ

**`window.turn` と `let turn` は別物**
- Firebaseモジュールからは `window.turn` でしか参照できない
- `turn++` する全箇所で `window.turn = turn` を明示的に代入すること

**`window.onlineRole` を使うこと**
- `let onlineRole` はモジュール外から見えないため常に `null`

**SSOT パターン**
- `_onlineTurnLogCapture !== null` の間は `addLog / updateHUD / screenShake / showDmgPopup` が DOM 出力をスキップ
- ホスト計算結果を Firestore 経由で両者が再生することで画面を同期

---

## 3. 現在残っているバグ・課題

### 音声（最優先）

1. **iPhone での音声動作未確認**
   - Netlify に正しいファイルをアップしてからテスト未実施
   - Web Audio API の iOS 対応は実装済みだが実機確認が必要
   - `crowd on fire.mp3`（ファイル名にスペース）が正しく fetch されるか確認

2. **`system,bgm` フォルダ名のカンマ**
   - `encodeURI` では `,` をエンコードしないため URL は正しくなるはず
   - ただし Netlify が `,` を含むパスを正しく扱うかサーバー側の確認が必要
   - 問題が出た場合はフォルダ名を `systembgm` 等にリネームして対応

### バランス調整（プレイテスト課題）

3. TITAN オーバーヒート: 攻撃-10%で十分か要確認
4. BLAZE 誘爆: 50%還元のバランス確認
5. 加速 15%コスト: 使われすぎないか確認

### オンライン機能

6. **切断ハンドリング未実装**: ゲスト離脱時にホストが永久待機になる
7. **ルームコード TTL なし**: Firestore にルームが蓄積され続ける
8. **相手のキャラが見えない**: キャラ選択画面でお互いの選択が非表示

---

## 4. 次セッションで真っ先に取り組むべきタスク

### 優先度 高

1. **iPhone 実機で音声テスト**
   - Netlify に正しいファイルをデプロイ済みであることを確認
   - オープニング BGM・効果音・リザルト BGM が全て鳴るか確認
   - 問題があればコンソールエラーをそのまま貼る

2. **`system,bgm` カンマ問題の確認**
   - Netlify のネットワークタブで `system,bgm` フォルダの fetch が 200 になるか確認
   - 404 が出る場合はフォルダ名をカンマなしに変更してコードも合わせる

### 優先度 中

3. **オンライン切断ハンドリング**
   - 30秒タイムアウト → タイトルに戻る最低限の処理

4. **バランスプレイテスト**
   - `autoMode` で TITAN・LYRA-X の対戦勝率を重点確認

### 優先度 低

5. **Firestore ルーム TTL**
   - `timestamp` フィールドを使ったクライアント側クリーンアップ

---

## 5. 作業時の注意事項

- **仕様書を先に読むこと**: `SYNTAX_WARS_GDD_v6.md`（v6.2 が最新）
- **ファイルは1つだけ**: `syntax_wars_v62.html` のみ編集
- **window.turn と let turn は別物**: ターンが変わる全箇所で `window.turn = turn` を忘れずに
- **window.onlineRole を使うこと**: `let onlineRole` は常に `null`
- **SSOT を壊さないこと**: `_onlineTurnLogCapture !== null` チェックを 4関数に維持
- **encodeURIComponent は使わない**: fetch パスには `encodeURI` を使うこと（カンマが壊れる）
- **AudioManager は async**: `playBGM` は async 関数。呼び出し元で await 不要だが Promise が返ることに注意
- **マルチエージェント inbox**: `/Users/kazutoshi/Desktop/AI関係/agent_hub/inbox/fighting_game.md` を確認すること

---

## 6. Netlify デプロイ手順（正しい方法）

Netlify のドラッグ＆ドロップは **`格ゲー` フォルダ全体**をドロップすること。

```
格ゲー/                       ← このフォルダごとドロップ
├── syntax_wars_v62.html
└── assets/
    ├── image/               キャラ画像 6枚（.PNG）
    └── sound/               音声ファイル 51ファイル
        ├── system,bgm/
        └── battle/
```

`syntax_wars_v62.html` 単体をドロップすると `assets/` が含まれず全て 404 になる。

---

## 7. オンライン対戦の実装詳細（変更なし）

### Firestore ルーム構造

```javascript
rooms/{roomCode}: {
  status: 'waiting' | 'matched',
  p1_ready: bool, p2_ready: bool,
  p1_char: string, p2_char: string,
  p1_turn_ready: bool, p2_turn_ready: bool,
  p1_action: string, p2_action: string,
  turn_result: {
    turn: number,
    p1State: {...}, p2State: {...},
    logs: [{text, cls}],
    audienceGauge: number,
    crowdOnFireNext: bool,
    gameOver: bool,
    winner: 'p1' | 'p2' | 'draw' | null
  } | null
}
```

### 過去に修正した主要バグ（再発防止メモ）

| バグ | 原因 | 修正 |
|------|------|------|
| ホストの計算が発火しない | `updateDoc` が await されず action がクリアされた | `async/await` 化 |
| turn_result が届かない（フリーズ） | `window.turn` が未定義 | `window.turn = turn` を全代入箇所に追加 |
| ゲスト無限ループ（TURN 2300+） | `_resultApplied` リセット後に古い snapshot が再ヒット | `_lastAppliedTurn` ガード追加 |
| 二重ログ・表示ズレ | ホストが DOM に書いてから Firestore 経由でも再生 | `_onlineTurnLogCapture` による DOM 抑制 |
| ゲスト視点が逆（YOU/OPPONENT混在） | ログが P1 視点で書かれている | `mirrorOnlineLogText()` 追加 |
| ホスト視点も逆 | `onlineRole` が常に null | `window.onlineRole` に修正 |
| iOS オープニングタップ不可 | `onclick` が iOS で発火しない | `ontouchstart` 追加 |
| 音声大合唱バグ | HTMLAudioElement の play/pause ウォームアップが失敗 | Web Audio API に完全移行 |
| 音声全 404 | 日本語ファイル名 / `encodeURIComponent` でカンマが壊れる | ファイル名を英数字化・`encodeURI` に変更 |
