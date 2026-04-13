# 作業ログ 2026-04-12

---

## 1. インタラクティブチュートリアル 改訂（CPU: SHADOW固定）

### 変更内容
前回セッションで実装したチュートリアルを全面改訂。

#### CPU キャラ変更
- TITAN → **SHADOW** に変更
  - 理由：TITANがSPEED 75になるのは設定的に不自然

#### STEPSアレイ再設計（全10ステップ）

| STEP | プレイヤー行動 | CPU行動 | 内容 |
|------|-------------|--------|------|
| 0 | 弱 | idle（案山子） | UI操作説明：①ボタン選択→②READY の2ステップ |
| 1 | 中 | idle | 中攻撃の説明 |
| 2 | 強 | idle | 速度差（SPEED差20以上）による確定外れ |
| 3 | 速度特攻 | idle | **NEW** 高速敵へのボーナスダメージ（SPEED75→+14） |
| 4 | 加速 | idle | SPEEDを上げてFAST状態へ |
| 5 | 強（リベンジ） | idle | forceHit：強攻撃が当たることを示す |
| 6 | ガード | 強（forceHit） | ジャストガード発動（ダメージ0・ゲージ+20%） |
| 7 | カウンター | 強（forceHit） | forceCounter：カウンター成功確定 |
| 8 | ガード破壊 | ガード | **NEW** ガード中の相手に命中→観客ゲージ+2→CROWD ON FIRE自然発動 |
| 9 | 固有技 | idle | CROWD ON FIRE中にフィニッシュ |

#### STEP 0〜5（案山子期間）
- CPU は `cpuAct: 'idle'` で何もしない
- `actionLabel` に `idle: '——（待機）'` を追加

#### CROWD ON FIRE の自然積み上げ
| イベント | 観客ゲージ加算 | 累計 |
|---------|-------------|-----|
| STEP 3 速度特攻（ボーナスあり） | +2 | 2 |
| STEP 6 ジャストガード | +3 | 5 |
| STEP 7 カウンター成功 | +3 | 8 |
| STEP 8 ガード破壊（ガード中命中） | +2 | **10 = MAX** |

→ STEP 8 解決中に自然発動。STEP 9 は最初から CROWD ON FIRE 状態。

#### SHADOW の pendingDouble 対策
CROWD ON FIRE 発動時、SHADOW passiveにより `cState.pendingDouble = true` が設定される。
STEP 9 の preSetup で即リセット：
```javascript
cState.pendingDouble = false;
cState.extraTurn = false;
```

---

## 2. バグ修正：チュートリアル後のCPU戦でボタンが操作不能

### 症状
チュートリアル終了後にCPU戦を開始すると、弱/中/強/ガードボタンが操作できない。
2ターン目はすべてのボタンが操作不能になる。

### 原因
チュートリアルの `_lockAllButtons()` がすべての `.action-btn` に `.disabled` を付与する。
`updateButtons()` はゲージコストのあるボタンしか `.disabled` を制御しないため、
弱/中/強/ガードの `.disabled` が次の戦闘でも残存していた。

### 修正
`startBattle()` の `updateButtons()` 呼び出し前に全ボタンの `.disabled` / `.tut-force` をクリア：
```javascript
document.querySelectorAll('.action-btn').forEach(b => b.classList.remove('disabled', 'tut-force'));
updateButtons();
```

---

## 3. バグ修正：低ゲージ時にゲージ消費ボタンが選択できる

### 症状
ゲージが不足している状態で、速度特攻・ガード破壊・回復封じ等のボタンを選択できてしまう。

### 原因
iOS Safari 等のモバイルブラウザで CSS `pointer-events:none` がタッチイベントを完全にブロックしないケースがある。
`selectAction()` 側にJS での無効チェックがなかった。

### 修正
`selectAction()` の先頭でボタンの `.disabled` クラスを確認し、付いていれば即 return：
```javascript
const btn = document.getElementById('btn-' + action);
if (btn && btn.classList.contains('disabled')) return;
```

---

## 4. デプロイ作業メモ

- ローカルテスト：`python3 -m http.server 8080` でLAN内サーバーを立てiPhone/iPadで検証
- デプロイ前にアセットフォルダ名を必ず確認（`assets/` であることを確認）
