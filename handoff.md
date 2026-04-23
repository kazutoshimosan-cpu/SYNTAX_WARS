# SYNTAX WARS v6.18 — 引き継ぎドキュメント

最終更新: 2026-04-23（ビジュアル方針メモ／**GDD v6.18**：属性・固有の使用後ゲージ+8 を明文化）

> **Netlify をやめて GitHub 側に寄せる話**（やさしいメモ）: 同フォルダの `Netlify_GitHub移行メモ.md`

---

## 0. 更新ログ

### 2026-04-23（ビジュアル方針：縦UI／キャラ絵の運用）

対象: 運用メモ（コード変更なし）

| 項目 | 内容 |
|------|------|
| 画面比率 | **縦（9:16）をメイン**想定。UIは「背景に余白を探す」より **上下にUI帯（暗幕グラデ＋必要なら半透明プレート）**で被せる方針が安全。 |
| キャラ画像差し替え | `assets/image/*.PNG` は **全キャラ揃ってから一括差し替え**（途中で1キャラだけ刷新すると世代差が出やすい）。 |
| NOVA刷新 | 「魔法っぽい紫オーラ」より **量子/重力寄りのSF表現**へ寄せる。**銀髪＋額の発光回路**を記号として固定。 |
| 背景素材 | Canvaは「背景の空気感をAI生成」より **仕上げ（色調整・雨/霧オーバーレイ・余白確保）**向き。背景単体はGemini側が現実的。 |
| デプロイ優先 | **次回デプロイ後**に、キャラ絵・背景・差し替えを少しずつ進める（本日は方針整理まで）。 |

### 2026-04-23（続き・GDD v6.18／属性・固有「使用後ゲージ+8」明文化）

対象: `SYNTAX_WARS_GDD_v6.md` / `CLAUDE.md` / `handoff.md` / `Syntax_Wars_作業ログ_貼り付け用.md`（`syntax_wars_v4html.html` の挙動は従来どおり）

| 項目 | 内容 |
|------|------|
| GDD **v6.18** | **属性付き攻撃・固有技**：**UI表記どおりのコストを先に差し引いた後**に、使用後ゲージ加算（基準 **+8%**、`addGauge` 経由で瀕死・OC減衰・キャラ係数の影響ありうる）を明文化。設計意図（使用促進・読み合いのノイズ）／コスト閾値は変わらず無限ループにならないこと／**NEXUS・NOVA** のゲージ回転個性としての位置づけ。 |
| 運用 | `CLAUDE.md` の最新 GDD 版を **v6.18** に同期。 |

### 2026-04-21（AUTO詳細CSV強化／NOVA閾値見直し／SHADOW調整）

対象: `syntax_wars_v4html.html`（AUTO BATTLE / AIロジック中心）

| 項目 | 内容 |
|------|------|
| AUTO詳細CSV | `abSaveDetailCsv()` の列を拡張し、各ターンについて **`resolveActions` 前（pre）と後（post）**の `AUD`/`CROWD次`/HP/ゲージ/SPEED/主要フラグを同一行に記録。`abOneBattle()` の `turns` を `pre/post` 構造に変更。 |
| NOVA（星屑砲） | 基礎ダメージを **35** に変更（損失HP×0.4は維持）。`uniqueDesc`/`UNIQUE_SUBTEXT`/`passiveDesc` の表記も追従。 |
| NOVA（瀕死ゲージ） | HP38以下のゲージ増加倍率を **×1.3→×1.25** に変更。 |
| NOVA（属性コスト軽減閾値） | **ゲージ70%→80%**で `25%→15%` に切り替わるよう、`atk25` 判定・`abSmartAct` のNOVA分岐・UI文言を更新。 |
| SHADOW（分身斬り×カウンター） | カウンターに合わせられた場合、斬撃は **60%**で通しつつ、カウンター返しは **本体HPを動かさず分身が受ける**挙動に調整。 |
| SHADOW（機動） | 命中時SPEED上昇を **弱+10**（中+8／強+6）へ調整（SPEED80以上は半減維持）。 |
| SHADOW（AI） | `accel` を薄くし、`unique` 寄せ＋カウンター過多抑制の方向で `abSmartAct` / `chooseCpuAction` を調整。 |

### 2026-04-21（続き・GDD v6.17／NOVA弱体／SHADOW案B＋CPU）

対象: `syntax_wars_v4html.html` / `SYNTAX_WARS_GDD_v6.md`

| 項目 | 内容 |
|------|------|
| NOVA（HP・瀕死） | **HP 95→90**。瀕死のゲージ増加 **+25%→+20%**（`addGauge` で `×1.2`）。瀕死・被ダメ-20%の判定を **`hp <= floor(maxHp×0.4)`** に統一（95HP時は従来の38以下と同等）。 |
| 案B（対NOVAメタ） | 全キャラ共通の対NOVA上乗せを、**SHADOW が CPU/AUTO で相手が NOVA のときはスキップ**（`chooseCpuAction` の NORMAL 内ブロック、`abSmartAct` 末尾）。SHADOW専用の NOVA 追いは **軽め（guardbk/sealatk 各3＋unique）**に留める。 |
| SHADOW CPU（一般） | EASY／NORMAL／`abSmartAct` で **weak 偏重を緩め**、`speedatk`・`mid`・`unique` の枝を増やす。 |
| SHADOW CPU（shPress） | 相手が **NEXUS（`learning`）／TITAN／BLAZE** のときだけ追加ウェイト（短ターン圧で削られる対面の底上げ）。AUTO では **対 NEX/TITAN/BLAZE で約+1〜2pp** 程度の改善。 |
| 次セッション | **`shPress` を廃止し相手別 `switch`** へ。**着手は NEXUS から**（人間が CPU にやっている「相手読み」を寄せる）。`Syntax_Wars_作業ログ_貼り付け用.md` の同日「続き」に詳細あり。 |

### 2026-04-21（追記・バランス最終寄せ：SHADOW固有コスト／LYRA回復／BLAZE戦術AI）

対象: `syntax_wars_v4html.html`

| 項目 | 内容 |
|------|------|
| SHADOW CPU（NEXUS専用） | `shPress`（learning/titan/blaze束ね）を廃止し、**まず NEXUS（learning）専用分岐**を追加（EASY／NORMAL／AUTO/HARD）。 |
| SHADOW（機動） | 通常攻撃命中時SPEED上昇（弱+10／中+8／強+6）で、**SPEED80以上でも半減しない**（上限100）。 |
| SHADOW（分身斬りコスト） | 分身斬り（固有）のコストを **50%→40%→35%**へ。UI表示・ボタン使用可否・AI・実消費を `uniqueGaugeCost()`/`canUseUnique()` で統一。 |
| LYRA-X（自動回復） | 自動回復を **+3→+5→+4**に調整（ターン終了時、プレイヤー/CPU共通）。パッシブ説明・ログ表示も追従。 |
| BLAZE（AI戦術） | 「**起爆準備はゲージ50%まで我慢**→起爆後は**属性/固有を優先**＋**たまにguardでフェイント**」へ（CPU EASY/NORMAL、AUTO/HARD）。 |

### 2026-04-20（AI/バランス調整：AUTOログ拡張・対NOVAメタ・CPU回復制限）

対象: `syntax_wars_v4html.html`（AUTO BATTLE / AIロジック中心）

| 項目 | 内容 |
|------|------|
| CPU回復制限 | **全キャラ・全難易度**で、CPUの `heal` は **TURN1〜2は選ばない／TURN3から許可**に変更（`chooseCpuAction` / `abSmartAct` / `abRandomAct` / `abPrompt`）。 |
| AUTO詳細CSV | `📋 詳細`（`abSaveDetailCsv()`）が **全キャラ×全試合×全ターン**を出力するように変更（従来は最初の5試合のみ `abDetailLogs` に記録）。 |
| NOVA挙動（試行） | NOVAの「属性連打」を可視化する目的で `abSmartAct` のNOVAを調整 → 勝率が極端に跳ねたため、**部分的に戻し**（弱/固有/カウンターの重みを緩め、ガードを少し混ぜる）。 |
| 対NOVAメタ（全キャラ共通） | **全キャラ共通**で、相手がNOVAのとき **序盤の回復封じ（turn<=3）／回復直後への再封じ（opp.lastAction==='heal'）／星屑砲ライン（opp.gauge>=50）への圧（sealatk＋guardbk）**を上乗せ。CPU（`chooseCpuAction` のNORMAL側）と AUTO（`abSmartAct`）の両方に適用。 |
| NOVA弱体（継続） | **序盤（HP48以上）被ダメ軽減を-5%**へ弱体（-10%から）。瀕死補正は **被ダメ-20%／ゲージ+25%（2026-04-21更新）**。属性攻撃コスト軽減は **80%以上で15%（2026-04-21更新）**。星屑砲は **基礎35＋損失HP×0.4／相手ゲージ-20%（CROWD -30%）／使用後+5%（2026-04-21更新）**。 |
| SHADOW強化 | 通常攻撃の命中（ガード半減含む）でSPEED上昇（最終：**弱+10**／中+8／強+6、**SPEED80以上は上昇量半分**）。速度差回避（絶影）後は次の**中/強/カウンター**が「反撃必中」。固有技は **影分身→分身斬り（影斬り相当×2.0＋身代わり1回）**へ（カウンター合わせ時の特例あり：2026-04-21更新）。 |
| BLAZE強化 | 起爆準備（primed）中は **爆裂拳（固有）コスト0%を1回**許可（UI/選択可否/AI条件も追従）。 |
| BLAZE耐久補強 | 起爆準備中の被ダメを **×0.8**。起爆準備後にカウンター反撃を受けた場合、反撃ダメージを **×0.7**。 |
| AI方針調整 | strong/mid偏重を緩め、**weakで貯めて属性/固有へ**寄せる方向で `abSmartAct`/`chooseCpuAction` を調整（やりすぎによる弱体が出たため段階的に戻しも実施）。 |
| SHADOW AI調整 | SHADOWが「弱で溜めて固有へ」勝ち筋に到達するため、**weak比率・固有ウェイト**を上げてゲージ50%到達率を改善（CPU/AUTO）。 |

### 2026-04-20（GDD §8-3：難易度別UX・学習導線 — v4 実装・GDD v6.14）

対象: `syntax_wars_v4html.html` / `SYNTAX_WARS_GDD_v6.md`（**GDD v6.15**）/ `CLAUDE.md` / `handoff.md` / `Syntax_Wars_作業ログ_貼り付け用.md`

| 項目 | 内容 |
|------|------|
| 仕様根拠 | GDD **§8-3**（勝因テンプレ・次手示唆ナビは採用しない／事実・リザルト学習／EASY=重さ付きナビ、NORMAL=事実のみ、HARD=ナビ無し 等）。 |
| 戦闘ナビ | **CPU・HARD** 非表示。**NORMAL** は SPEED 差・デバフ・CROWD 状態など**盤面事実のみ**（`#easy-hint.mode-normal`）。**EASY** は従来の CPU 説明＋**選択の重さラベル**（`ACTION_WEIGHT_NAV`）。**HINT** トグルは EASY/NORMAL の CPU 戦で表示。 |
| コマンド説明 | `ACTION_SUBTEXT` / `UNIQUE_SUBTEXT` に弱中強ガードを追加し、各コマンドに **【重さ】** 行を付与。 |
| リザルト | **全難易度共通レイアウト**で CROWD 要約を常時表示。**相手キャラ固有**はキーワード行＋**タップ折りたたみ**（2P は P1/P2 それぞれ）。**HARD**（CPU/オンライン）は折りたたみ内にカウンター成功回数・腐食被ダメ合計。 |
| 集計 | `matchStats`（`applyCounter` 成功・腐食 -3HP ごとに加算、`startGame` でリセット）。 |
| GDD/運用 | **v6.14**（8-3 実装反映済み明記）。`CLAUDE.md` 最新版を v6.14 に同期。 |

### 2026-04-20（HUD：EASYヒント／CROWD帯／AUDIENCE表示切替／ジャスト時SPEED-10）

対象: `syntax_wars_v4html.html` / `SYNTAX_WARS_GDD_v6.md`（**GDD v6.12**）

| 項目 | 内容 |
|------|------|
| EASYヒント位置 | `#easy-hint`（＋`HINT`トグル）を **READY直下**へ移動（旧：ボタン最下部）。 |
| CROWD告知位置 | `#battle-context-strip` を **キャラ直下（AUDIENCE直上）**へ移動。 |
| AUDIENCE表示 | `crowdOnFireNext` 中は **AUDIENCE枠を非表示**にし、CROWD帯のみを同じ縦スロットに表示。 |
| 文言 | 青バッジを **`⚡ 次ターン両者同時行動`** に変更。CROWD関連ログもユーザー向け表現に寄せた。 |
| バグ修正 | 強攻撃の `SPEED-10` が **`dealDamage()` 前に適用**されジャスト後も降速する問題を修正（最終ダメージが0なら不発）。 |
| GDD | **v6.12**：ジャストガード成立時は強攻撃の `SPEED-10` を付与しない旨を明文化（強×ガードは常にジャスト扱い）。 |

### 2026-04-19（続き：OC同士ジャスト・ログ・スマホ）

対象: `syntax_wars_v4html.html` / `SYNTAX_WARS_GDD_v6.md`（**GDD v6.11**）

| 項目 | 内容 |
|------|------|
| OC同士ターン | 前ターンに**双方がオーバークロック**→次ターンが**双方2回行動**のとき、`ohkMirrorTurn`。ガード時 `guardJustCharges = 2`、ジャスト成功ごとに減算、0で `guardThisTurn` 解除。 |
| フラグ | `doubleFromOverclock`（OC起因）／`guardJustCharges`。オンライン `serializeState` 済。 |
| 絶影×CROWD | `pendingDouble` が**既にある**ときは `doubleFromOverclock` を落とさない（同一ターンで OC 後に CROWD が乗る誤消し防止）。 |
| フラグ寿命 | `doubleFromOverclock` は `resolveActions` **末尾**で「その解決で2回行動だった側」だけクリア（ターン開始時の双方OC判定を維持）。 |
| ログバー | 直近 **6行**（旧3行）。OC／絶影の2回目の前に `▶ 2回目 …`（`log-action`）。SPEED70追撃の前に `▶ 追撃 …`。 |
| スマホUI | キャッチコピー短縮＋戦略寄り、`char-catchphrase` 折り返し、`fighter-name` ellipsis。 |
| キャッチフレーズ（確定） | 6キャラの `CHARS.catchphrase` を句点なし・2行前提で整理。全角スペース（`　`）を `<br>` に置換して選択画面で必ず2行表示。 |
| アクションPOP視認性 | キャラ上の POP（JUST GUARD / GUARD BREAK / COUNTER / MISREAD）を大きく、表示時間も延長して「何が出たか」読めるように調整。 |
| EASY（VS CPU）体験強化 | `chooseCpuAction()` の easy 分岐を「キャラの持ち味＋短いフェーズ（3ターン巡回）」に変更（相手状況は見ず自分状況のみ参照）。`#easy-hint` に `CPU特徴/CPU傾向（直近4手つき）/ボタン説明` を表示し、視認性のため背景/枠/影も追加。`actionHistory` は EASY時のみ 9件保持。CPUの回復は TURN1 では選ばない（CPUのみ）。 |

### 2026-04-19（改修指示書 v1 + UI改善）

対象ファイル: `syntax_wars_v4html.html`

**改修指示書 v1（全6項目）**

| # | 内容 | 備考 |
|---|------|------|
| 1 | 「ガード破壊」→「ガード貫通」に全箇所リネーム | ボタン・ログ・チュートリアル・AI コメント含む |
| 2 | GUARD BREAK 誤表示修正（ガード中でないとポップ出ない） | v4 では既に修正済みを確認 |
| 3 | アクションポップラベル追加 | JUST GUARD ✨ / GUARD BREAK 💥 / COUNTER ⚡ / MISREAD をパネル上に表示 |
| 4 | コマンドサブテキスト（EASYモード） | `ACTION_SUBTEXT` / `UNIQUE_SUBTEXT` 定数。HINT ON/OFF トグルボタン付き |
| 5 | キャラ選択画面改修 | 丸アイコン＋キャッチコピー、PASSIVEキーワード行、FIGHT/BACK フッター固定 |
| 6 | キャラ固有チュートリアルポップ | セッション内初回のみ（`_seenCharHints` Set で管理、localStorage 不使用） |

**UI改善（同日追加）**

| 内容 | 詳細 |
|------|------|
| PASSIVEパネル簡略化 | `passiveDesc`（長文）を削除。PASSIVEキーワード行 + SPECIAL MOVE 1行のみ表示 |
| SPECIAL MOVE 説明を簡潔化 | 各キャラの `uniqueDesc` を仕様書準拠の簡潔1行に更新 |
| ポップ表示時間延長 | JUST GUARD / GUARD BREAK / COUNTER → 1.2s、MISREAD → 0.8s、DEFENDED → 0.6s |
| EASYヒント文字強調 | `#easy-hint`: font-size 11px → 14px、font-weight 700 |
| キャラカード画像の切り取り修正 | `object-position` を `c.imgPosition` から適用（SHADOW/NOVA → top、TITAN → 20% top）|
| 詳細パネル文字色改善 | PASSIVEキーワード → `#c0e8ff`（明るい水色）、SPECIAL MOVE → `#e0c87a`（ゴールド）|
| 詳細ラベル強調 | PASSIVE / SPECIAL MOVE 見出しに太字 + 下線ボーダー追加 |

---

## 1. 全体的な進捗

### 完成している機能

| 機能 | 状態 |
|------|------|
| ターン制戦闘（同時選択・同時解決） | ✅ 完成 |
| 6キャラクター実装（NEXUS-7/SHADOW/NOVA QUEEN/TITAN/LYRA-X/BLAZE） | ✅ 完成 |
| キャラ固有スキル（TITAN オーバーヒート、BLAZE 誘爆 等） | ✅ 完成 |
| オーディエンスゲージ（CROWD ON FIRE） | ✅ 完成（**v6.12**: CROWD中はAUDIENCE枠を隠して告知帯のみ表示） |
| ジャストガード | ✅ 完成（**v6.11**: 双方OC次ターンは各自ジャスト枠2回 / **v6.12**: ジャスト時は強攻撃のSPEED-10不発） |
| 難易度選択（EASY/NORMAL/HARD） | ✅ 完成 |
| 難易度別UX・学習導線（GDD §8-3） | ✅ **v4 実装済**（EASY/NORMAL 戦闘ナビ、HARD ナビ無し、リザルト CROWD＋キャラ折りたたみ、HARD 計測、コマンド【重さ】） |
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

### HTML エントリ（2026-04 時点の運用）

| ファイル | 役割 |
|---------|------|
| `syntax_wars.v2html.html` | **現行 Netlify デプロイ**。のちに廃止予定 |
| `syntax_wars_v4html.html` | **開発・修正の主軸（V4）**。仕様は GDD **v6.18**（タイトル行確認。§8-3 UX・属性/固有+8 明文化 含む）。切替後にデプロイ本体へ |

旧メモの `syntax_wars_v62.html` 単体運用は廃止。いずれも 1 ファイル完結（HTML + CSS + JS）、行数は **約 5400 行**（V4・2026-04 時点）。

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
> **ネットワーク用 URL**（`fetch` / `new Audio` / `<audio src>`）は論理パスを `/` で分割し、**セグメントごと** `encodeURIComponent` して再結合する（`system,bgm` → `system%2Cbgm`）。Safari がカンマをパス区切りと誤解し `system/bgm` になるのを防ぐ。**`buffers` / `rawBuffers` のキー**は従来どおり論理パス（カンマあり）。

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

### 音声（実機が手元にあれば確認）

1. **iPhone / Safari でのオープニング BGM**
   - セグメントエンコード（`urlForRequest` / プリロード `fetch`）で **404（`system/bgm`）を解消**済み。**V4 をデプロイしたビルド**で Network に `system%2Cbgm` が出るか確認
   - まだ鳴らない場合は **キャッシュ無効リロード**、または **フォルダ名を `system_bgm` 等にリネーム**してパスを一本化（ホストが `%2C` を嫌う場合の最終手段）

2. **`crowd on fire.mp3`（ファイル名にスペース）**
   - 同じセグメントエンコードで問題にならない想定。実機で fetch が 200 か確認

### バランス調整（当面は据え置き）

3. 初回デプロイ前に詰め済み。**しばらく数値は触らない**方針（人間同士の対戦データが溜まるまで／CPU 強めで現状運用）。
4. 参考メモ（再開するとき用）: TITAN OH -10%、BLAZE 誘爆 50%、加速 15% コスト

### オンライン機能（知り合い同士プレイ前提）

5. **切断ハンドリング未実装**: 技術的には残課題だが、**声掛けして遊ぶ運用では当面起きにくい／優先しない**。
6. **ルームコード TTL なし**: Firestore にルームが蓄積され続ける（低優先度でよいなら後回し）
7. **相手のキャラが見えない**: キャラ選択画面でお互いの選択が非表示

---

## 4. 次セッションで真っ先に取り組むべきタスク

### 優先度 高（現在の主戦場）

0. **SHADOW CPU：相手別戦術（新チャット推奨）**  
   - 現状：`syntax_wars_v4html.html` 内で **`shPress`（learning／titan／blaze まとめ）**＋案B（SHADOW×NOVA は共通対NOVAメタ除外）。  
   - **次**：`shPress` をやめ、**`opp.passive` ごとに分岐**。まず **NEXUS（learning）** だけウェイトを練り、AUTO（`sw_auto`/`sw_detail`）で検証 → TITAN → BLAZE。  
   - 根拠データ：Downloads の `sw_auto_2026-04-21*.csv` / `sw_detail_2026-04-21*.csv`（SHADOW 全体期待はまだ約40%、対 NEX は約30%前後）。

1. **バトル中のわかりやすさ・体験向上**（V4 で着手済みのライン）
   - タイトル・チュートリアルは完了済み（2026-04-19 時点）
   - HUD／ログ／同時解決の読み取り／フィードバック（ダメージ・状態）など、**プレイ中に迷子にならない UI**

#### 参考メモ（次回検討：EASY限定の「匂わせヒント」案）

方針: **答えを提示しない／ユーザーに考えさせる**前提で、判断材料だけ増やす（EASYのみ）。

- **相手の狙いの可視化（おすすめ）**: 直近2〜3ターンの相手行動から、ラベルを1個だけ表示（例「相手: 強寄り / 守り寄り / ゲージ温存 / 回復狙い」）。
- **自キャラの勝ち筋トラッカー**: キャッチフレーズの2行に対応する「今どっち側か」を点灯（例 SHADOW「回避側」、NEXUS「学習済み」等）。行動指示ではなく状態の意味付け。
- **危険アラートだけ出す**: 特定状況で「警戒」だけ出す（例 SHADOW相手に強→回避されるかも、BLAZE起爆中→次パンチ危険）。押すボタンはプレイヤーが決める。
- **ボタンの匂わせ説明を盤面依存で差し替え**: EASYのときだけサブテキストを状況で一言変える（断定しない表現）。

戦闘中の1行ヒント例（EASY限定・断定しない）
- NEXUS: 被弾してもいい まず解析を通せ
- SHADOW: 強が来そうなら回避で稼げ
- NOVA: 溜まったら属性 追い詰められたら星屑砲
- TITAN: 受けて進め 返しの一撃で取れ
- LYRA: slowを通せ 腐食で焦らせろ
- BLAZE: 起爆が先 仕上げは爆裂

### 優先度 中（実機が手元にあれば）

2. **iPhone 実機で音声テスト**（V4 をデプロイしたタイミングで）
3. **`system,bgm` カンマ問題の確認**（ネットワークタブで 200 か）

### 優先度 低／任意

4. **Firestore ルーム TTL**（ルーム蓄積が気になったら）
5. **オンライン切断ハンドリング**（野良マッチ等に広げる段階で再検討）
6. **バランス再開**（人間同士ログが十分溜まってから）

---

## 5. 作業時の注意事項

- **仕様書を先に読むこと**: `SYNTAX_WARS_GDD_v6.md`（**v6.18** が最新。タイトル行を確認）
- **編集する HTML**: 修正は **`syntax_wars_v4html.html`**。デプロイ切替までは本番が V2 の可能性あり — リリース時は Netlify のエントリを V4 に合わせる
- **window.turn と let turn は別物**: ターンが変わる全箇所で `window.turn = turn` を忘れずに
- **window.onlineRole を使うこと**: `let onlineRole` は常に `null`
- **SSOT を壊さないこと**: `_onlineTurnLogCapture !== null` チェックを 4関数に維持
- **音声のネットワーク URL**: 論理パスを **`path.split('/').map(encodeURIComponent).join('/')`** で組み立てる（`AudioManager` の `urlForRequest` とプリロード `fetch` が一致していること）。**内部キーは論理パスのまま**
- **AudioManager は async**: `playBGM` は async 関数。呼び出し元で await 不要だが Promise が返ることに注意
- **マルチエージェント inbox**: `/Users/kazutoshi/Desktop/AI関係/agent_hub/inbox/fighting_game.md` を確認すること

---

## 6. Netlify デプロイ手順（正しい方法）

Netlify のドラッグ＆ドロップは **`格ゲー` フォルダ全体**をドロップすること。

```
格ゲー/                       ← このフォルダごとドロップ
├── syntax_wars.v2html.html   ← 現行デプロイ想定（2026-04-19）
├── syntax_wars_v4html.html   ← 開発主軸。切替後にデプロイ本体へ
└── assets/
    ├── image/               キャラ画像 6枚（.PNG）
    └── sound/               音声ファイル 51ファイル
        ├── system,bgm/
        └── battle/
```

**`格ゲー` フォルダ以外や、`assets/` なしの HTML だけをドロップすると 404 になる**（従来メモの単体ファイル名は環境により異なる）。

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
| 音声 404（`system/bgm`） | Safari が `system,bgm` のカンマをパス区切りと誤解釈 | セグメントごと `encodeURIComponent`（`system%2Cbgm`）。論理キーはカンマありのまま |
