# Syntax Wars — 作業ログ（貼り付け用）

**使い方:** 下の「今日のブロック」に、まとめた文章をそのまま貼る。日が変わったら **新しい日付のブロックをコピー**して、上に足していくと古い順に残る。

---

## 2026-05-02（タイトル魅せ方改善／BGM軽量化／タイトルBGM起動修正）

### 今日やったこと（ここに貼る）

担当エージェント
* Codex

タイトル離脱の仮説整理
* 今日も何回かアクセスはあったが、計測上はタイトル付近で止まっている可能性がある。
* ゲーム内容が悪いというより、初見ユーザーが「これは何をするゲームか」「最初にどこを押せばいいか」を掴む前に離脱している仮説。
* 世界観を読ませる前に、タイトルで「同時選択して読み合うゲーム」だと一瞬で伝える方向へ寄せる。

BGM軽量化
* 音がなかなか出ない原因として、BGMファイルの重さを確認。
* SEは多くが数十KB〜100KB前後で軽く、主因はBGMと判断。
* 重いBGM 5本を圧縮。
  * `title.mp3`: 約5.4MB → 約1.9MB
  * `characterchoosing.mp3`: 約6.2MB → 約1.8MB
  * `battle1.mp3`: 約2.5MB → 約845KB
  * `battle2.mp3`: 約3.1MB → 約1.0MB
  * `battle3.mp3`: 約3.1MB → 約1.1MB
* 対象5本合計は約20MBから約6.7MBへ軽量化。
* `result.mp3`、`opening.mp3`、各SEは十分軽いため今回は触らず。

タイトルBGM起動修正
* タイトル画面は最初から表示されているが、初回表示時には `playBGM('title')` が呼ばれていなかった。
* iPhone/Safariではユーザー操作前の自動再生ができないため、タイトル画面の最初のタップ/クリックをきっかけにタイトルBGMを開始する処理を追加。
* 追加の `SOUND ON` ボタンは出さず、既存のタイトル操作で自然に音が鳴る形にした。

タイトルの読み合いデモ
* タイトル説明文の下、ボタン群の上に `LIVE SIMULATION / 読み合いデモ` を追加。
* 実戦スクショではなく、軽いHTML/CSSのミニUIで「YOU / ENEMY が同時に選ぶ → 結果が出る」を見せる形。
* 回復封じなど次ターン概念が絡むものは初見には難しいため、タイトルデモからは除外。
* デモは以下の3パターンを約2.6秒ごとに切替。
  * `GUARD vs HEAVY` → `JUST GUARD!`
  * `COUNTER vs STRIKE` → `COUNTER HIT!`
  * `FAST vs ATTACK` → `SPEED SHIFT!`
* 海外流入も想定しつつ、日本語ユーザーにも伝わるよう、結果説明は短く日英併記にした。

### 触ったファイル（わかれば）

- `syntax_wars_v4html.html`
- `assets/sound/system,bgm/BGM/title.mp3`
- `assets/sound/system,bgm/BGM/characterchoosing.mp3`
- `assets/sound/system,bgm/BGM/battle1.mp3`
- `assets/sound/system,bgm/BGM/battle2.mp3`
- `assets/sound/system,bgm/BGM/battle3.mp3`
- `handoff.md`
- `Syntax_Wars_作業ログ_貼り付け用.md`

### 次にやること（メモ程度でOK）

- GitHub Pages反映後に、`user_events` で `page_open → title_button_cpu → cpu_single_start_click → character_select` の落ち方を見る。
- タイトルデモで本当に伝わるかはまだ未確定。情報量が多い、ボタンが下がりすぎる、英語/日本語が読みにくい場合は調整。
- さらに改善するなら、タイトルに `START: VS CPU EASY` の直通ボタンを作り、難易度画面を飛ばしてキャラ選択へ送る案を検討。

### 気づいたこと・不具合

- タイトルで止まるアクセスは、ゲーム本編以前に「入口の理解」で落ちている可能性がある。
- 初見タイトルでは世界観よりも「ルールの一口試食」が重要。
- 回復封じ、腐食、CROWD、ゲージなどは面白いが、タイトルで見せるには重い。まずは JUST GUARD / COUNTER / SPEED 程度に絞るのがよさそう。

---

## 2026-04-30（GitHub Pages導線改善／計測／SNS運用準備）

### 今日やったこと（ここに貼る）

担当エージェント
* Codex

GitHub Pages導線の見直し
* GitHub Pages公開後にユーザーアクセス/プレイ完走が落ちているように見えたため、まず公開URL・入口・プリロード・計測の順に確認。
* 旧Netlifyリンクをすぐ直せない前提で、SNS/Instagramから新URLへ再誘導する方針に整理。
* タイトル画面下に英語案内 `First time? Start with VS CPU → EASY. / Add to Home Screen on mobile.` を追加。
* 初期表示をオープニングからタイトル直行へ変更。初見ユーザーがすぐ `VS CPU` を選べる導線にした。
* 旧オープニングは廃止し、世界観はタイトルの `INTRO / WORLD` ボタンから任意で読める形へ変更。
* `INTRO / WORLD` は EN/JP 切替つき。世界観は「SYNTAX=地下闘技場＋企業の戦闘データ市場」「観客は読み合いに熱狂する」「歓声が戦士の価値を押し上げる」に整理。

初回ロード・音声
* 起動時に全音声を読む構成をやめ、初回に必要な最小限だけ読む形に変更。
* バトルBGM、技SE、各種SEはタイトル操作後または再生時に取得する方針。
* オープニングを初期表示から外したため、旧オープニング音声も初回読み込み対象から除外。
* 初回音声ロードは約45MBから約5.4MBへ軽量化。

計測
* Firestoreに `user_events` コレクションを追加して、途中行動を保存。
* 記録イベント: `page_open`, `screen_view`, `title_button_cpu`, `title_button_2p`, `title_button_online`, `title_button_tutorial`, `title_button_intro`, `intro_lang_en`, `intro_lang_jp`, `character_select`, `character_confirm`, `battle_start`, `match_result` など。
* Firebase Rulesで `user_events` は `create` のみ許可、read/update/deleteは禁止。
* 初回CSVでは自分の確認アクセス混在あり。`page_open` 15、`match_result` 1。完走セッションは `VS CPU → EASY → NOVA QUEEN vs LYRA-X → 12ターン敗北`。

分析CSV
* ローカル分析用に `scripts/export_firestore_events.js` を作成。
* サービスアカウントJSONはリポジトリ外のデスクトップに置き、スクリプト実行時にパス指定。
* 出力: `user_events_raw.csv`, `event_counts.csv`, `funnel_summary.csv`, `session_paths.csv`。
* 秘密鍵JSONはGitHubに入れない。

Instagram/SNS運用
* 旧小説アカウントを、SYNTAX専用ではなく「個人制作ゲーム全般」のアカウントへ移行する方針。
* Instagramは海外フォロワー向けに英語投稿・英語プロフィール・プロフィールリンク運用。
* 投稿本文内URLはクリック不可のため、プロフィールリンクに GitHub Pages URL を設定。
* `assets/posters` の6キャラポスターから、固定投稿用4:5画像 `docs/instagram/generated/syntax_wars_fixed_post_cover.png` を作成。
* キャプションは英語で `Play link in bio.` 前提に整理。

GitHub
* 以下のコミットを `main` にpush済み。
  * `ea5dc50` Lighten startup preload and add title analytics
  * `11d089f` Use English title start hint
  * `9f76c66` Move intro behind title screen
* 分析スクリプト、CSV、Instagram生成画像はローカル運用物として未コミットの可能性あり。

### 触ったファイル（わかれば）

- `syntax_wars_v4html.html`
- `scripts/export_firestore_events.js`
- `docs/analytics/export/*.csv`
- `docs/instagram/generated/syntax_wars_fixed_post_cover.png`
- Firebase Firestore Rules（手動）
- `handoff.md`
- `Syntax_Wars_作業ログ_貼り付け用.md`

### 次にやること（メモ程度でOK）

- 数時間〜1日後に `user_events` を再エクスポートし、タイトル直行後の `page_open → title_button_cpu → battle_start → match_result` を見る。
- 自分のアクセスが混ざるため、投稿後の時間帯や端末サイズを見ながら判断する。
- 必要なら `Tap any button to enable sound` など音の案内を後で検討。
- Instagramは固定ポスト、ストーリー、キャラ紹介投稿で継続誘導。

### 気づいたこと・不具合

- Instagramの投稿本文内リンクはクリック不可。プロフィールリンク誘導が必要。
- iPhone/iPadアプリではプロフィールリンク追加やピン留めUIが不安定な場合がある。
- 初期導線では世界観より「すぐ遊べる」ことが重要。世界観は任意閲覧に回す方が分析しやすい。

---

## 2026-04-30（キャラ選択・結果画面ポスター／画像軽量化）

### 今日やったこと（ここに貼る）

担当エージェント
* Codex

キャラ選択画面のビジュアル強化
* `assets/posters/` に追加した名前なし9:16ポスターを、実装しやすい英字名へ統一。
  * `nexus7_9x16.png`
  * `shadow_9x16.png`
  * `nova_9x16.png`
  * `titan_9x16.png`
  * `lyra_9x16.png`
  * `blaze_9x16.png`
* PC版のキャラ選択では、キャラカード一覧の右側に選択中キャラの9:16ポスターを表示。
* スマホ版は最初にポスターが出ると重く感じたため、初期表示ではカード一覧を先に出し、キャラ選択後だけカード下に小さめポスターを表示する形へ調整。

画像軽量化
* 丸アイコン用に `assets/image/thumbs/` を作成。
  * `*_thumb.jpg` 6枚を追加。
  * キャラ選択カード、バトルHUDの丸アイコン、起動時プリロードをサムネイル参照へ変更。
  * 丸アイコン分の初期画像読み込みは約12MBから約180KB程度まで軽量化。
* スマホ用ポスターとして `assets/posters/mobile/` を作成。
  * `*_poster_mobile.jpg` 6枚を追加。
  * スマホでは約93〜130KBの軽量ポスター、PCでは元の高解像度9:16ポスターを使う分岐にした。

結果画面
* `WINNER! / ELIMINATED / DRAW` の下、戦績表の上に全身ポスター枠を追加。
* 勝利時は勝ったキャラの高解像度ポスター、敗北時は相手キャラ、2PではP1/P2勝者、DRAWでは自分側キャラを表示。

GitHub
* 以下のコミットを `main` に push 済み。
  * `29adfc7` Add character select poster previews
  * `4e85e3f` Tune mobile character select posters
  * `cf6503c` Use lightweight character thumbnails
  * `873f5f4` Add lightweight mobile character posters
  * `dbc75fb` Show winner poster on result screen

### 触ったファイル（わかれば）

- `syntax_wars_v4html.html`
- `assets/posters/*_9x16.png`
- `assets/posters/mobile/*_poster_mobile.jpg`
- `assets/image/thumbs/*_thumb.jpg`
- `handoff.md`
- `Syntax_Wars_作業ログ_貼り付け用.md`

### 次にやること（メモ程度でOK）

- iPhone実機で、キャラ選択の初期表示・選択後ポスター・結果画面ポスターの重さを確認。
- 結果画面の高解像度ポスターが重ければ、結果画面もスマホだけ軽量版に切り替える。
- 勝利画面のポスター位置・サイズが気になれば、タイトル下/戦績上の高さを微調整。

### 気づいたこと・不具合

- スマホWebでは2〜3MB級の画像を選択画面で読むと、見た目は良くても初動が重く感じやすい。
- PCは高解像度ポスターがかなり映える。スマホは軽量版でも雰囲気は十分出る。

---

## 2026-04-26（通常攻撃の技シーン画像集／インスタ投稿テンプレ）

### 今日やったこと（ここに貼る）

画像素材の整理（コード変更なし）
* `syntax wars 画像集/` に、各キャラ「**弱攻撃1枚＋追加1枚（中 or 強）**」の技シーン画像を作成して格納（キャラ別フォルダ）。
* インスタ優先で、実際の画像を確認して「投稿で映える構図か／文字を置ける余白があるか／全体の統一感」をチェック。
  * 縦長で迫力があり、色味もキャラ別に整理されていて **インスタ運用に向く**（BLAZE=橙、LYRA=黄緑、TITAN=青、NOVA=紫、等）。

インスタ投稿テンプレ（運用案）
* 1キャラ=1カルーセル（2枚）で量産する方針。
  * **表紙（1枚目）＝POWER（中/強）**：派手でスクロール停止率が上がる想定
  * **2枚目＝BASIC（弱）**
* 文字は全キャラ共通で **上部固定**が事故りにくい（下はエフェクトが強い画像が多い）。
  * 入れる文字は最小限（キャラ名＋BASIC/POWER）。説明はキャプションへ。
* 推奨比率：フィードは **4:5（1080×1350）** へ寄せると面積を最大化できる（素材は縦長なので相性良い）。

キャラ別：BASIC/POWER の割り当て（暫定）
* BLAZE：BASIC=`fireball.PNG` / POWER=`火炎放射器.PNG`
* LYRA：**BASIC=`酸液噴射.png` / POWER=`変異爪.png`（※このキャラだけ逆）**
* TITAN：BASIC=`鉄拳.png` / POWER=`ギガントプレス.png`
* NEXUS-7：BASIC=`ナノブレード.PNG` / POWER=`ビーム.PNG`
* NOVA：BASIC=`エナジーボール.PNG` / POWER=`次元裂き.PNG`
* SHADOW：BASIC=`くない投げ.PNG` / POWER=`影斬り.PNG`

現状の運用状況
* まだ一昨日作った **ポスター投稿を進めている最中**。通常攻撃画像の投稿は次の弾として準備。

### 触ったファイル（わかれば）

- `Syntax_Wars_作業ログ_貼り付け用.md`
- `handoff.md`

### 次にやること（メモ程度でOK）

- インスタ用に、キャラごとに `basic.png` / `power.png` の **命名統一**を検討（投稿・実装で迷子防止）。
- キャプションの定型（コピペ用）とハッシュタグ固定セットを作る。

### 気づいたこと・不具合

- （追記なし）

---

## 2026-04-23（ビジュアル方針メモ／デプロイ後に絵を少しずつ／GDD v6.18）

### 今日やったこと（ここに貼る）

オンライン（ネット対戦）体験差の修正（`syntax_wars_v4html.html`）
* オンラインは「**ホストのみ `resolveActions`**」で、ゲストは `turn_result` の適用のみ（SSOT）という構造のため、ローカルと比べて **VFX/UI/音**が欠けていた。
* **VFXの欠け**：ダメージを与えてもパネルが震えない／ダメ数字が出ない／カウンターやガード貫通の表示が揃わない。
  * `turn_result.fx` を追加し、ホスト計算中に **シェイク・ダメポップ・アクションPOP・中央フラッシュ**をイベントとしてキャプチャ→両端末で同順再生。
  * ゲスト側は左右が入れ替わるため、`fx` の `isPlayer` を **ゲスト時だけ反転**して再生（COUNTER/MISREADが逆になる問題を解消）。
* **オーディエンスゲージUIの欠け**：ゲスト側で貯まらない（実値は同期しているが見た目が更新されない）。
  * `syncAudienceChrome()` で **バー幅・数値も `audienceGauge` から描画**するように修正。
  * `addAudience()` はオンラインホスト計算中に DOM 更新を抑制（結果適用側で揃える）。
* **効果音（SE）の欠け**：ホストだけ鳴ってゲストが無音になりやすい。
  * `turn_result.se` を追加し、ホスト計算中はSEを鳴らさずキャプチャ→両端末で再生（ホスト二重再生も抑止）。

GitHub移行／公開先の切替
* GitHubリポジトリ：`SYNTAX_WARS`（`origin`）にコミット・push。
* Netlifyは **月間限度額超過**でプロジェクトが Paused になり利用不可に。
* 公開を **GitHub Pages** に切替。公開URL：`https://kazutoshimosan-cpu.github.io/SYNTAX_WARS/`
  * ルート `/` で V4 を開くため `index.html` を追加（`syntax_wars_v4html.html` へ誘導）。
  * Netlify用に `_redirects` も追加（将来再開時の保険）。
* Pages移行後、Consoleに赤が出た件は **キャッシュ**起因（`?v=1` や強制リロードで解消）。現状の赤は `favicon.ico` 404 程度で動作影響なし（放置）。

ビジュアル／運用方針（コード変更なし）
* **縦（9:16）メイン**の前提でUI設計メモ：背景の「空き」より **上下のUI帯（暗幕グラデ＋必要なら半透明プレート）**で読みやすさを担保する方針。
* キャラ画像（`assets/image/*.PNG`）は **全キャラ揃ってから一括差し替え**（途中で1キャラだけ刷新すると絵の世代差が出やすい）。
* NOVAの方向性：**魔法っぽい紫オーラ**より **量子/重力寄りのSF表現**へ寄せる。**銀髪＋額の発光回路**を記号として固定する案を採用。
* 背景：Canvaは「背景の空気感をAI生成」より **仕上げ（色調整・雨/霧オーバーレイ・余白）**向き。背景単体生成はGemini側が現実的。
* **次回デプロイ後**に、キャラ絵・背景・差し替えを少しずつ進める（本日は方針整理まで）。

GDD／ゲージ表記（ドキュメント中心・V4の実装は従来どおり）
* **GDD v6.18**：属性・固有について **表記コストを先に支払った後**に **使用後ゲージ+8（基準、`addGauge` で係数ありうる）** を明文化。設計意図（使用促進・読み合いのノイズ）／閾値は据え置きで無限ループにならないこと／**NEXUS・NOVA** のゲージ回転個性。
* 試行：`syntax_wars_v4html.html` から属性・固有の **+8 を一度削除**して AUTO 確認 → **人間同士の読み合いでは従来の +8 がよいスパイス**として **復帰**。仕様は上記 GDD に寄せて記録。
* `CLAUDE.md` の GDD 最新版を **v6.18** に同期。`handoff.md` も **v6.18** 表記・更新ログ追記。

### 触ったファイル（わかれば）

- `handoff.md`
- `Syntax_Wars_作業ログ_貼り付け用.md`
- `SYNTAX_WARS_GDD_v6.md`
- `CLAUDE.md`
- `syntax_wars_v4html.html`（オンライン体験差：VFX/SE/Audience同期）
- `index.html`（GitHub Pagesの入口：V4へ）
- `_redirects`（Netlify用の保険）
- `.gitignore`（`.ai/` を除外）

### 次にやること（メモ程度でOK）

- デプロイ後：各キャラ **A枠（顔アイコン）／B枠（カットイン）**の採用を決め、`assets/image/*.PNG` を一括更新。
- NOVA：量子表現の **参照プロンプト固定**（銀髪＋額回路＋禁止事項：炎/魔法オーラ）。

### 気づいたこと・不具合

- （追記なし）

---

## 2026-04-21（AUTO詳細CSV強化／NOVA閾値見直し／SHADOW調整）

### 今日やったこと（ここに貼る）

AUTO BATTLE / ログ（`syntax_wars_v4html.html`）
* `📋 詳細`（`abSaveDetailCsv()`）を拡張：`resolveActions` **前後**のスナップショットを同一行に出す（`AUD`/`CROWD次`、各種状態フラグ、HP/ゲージ/SPEEDの前後）。
* `abOneBattle()` のターン記録を `pre` / `post` 構造に変更（詳細CSVの列増に追従）。

NOVA QUEEN（数値・閾値）
* 星屑砲：基礎ダメージを **40→35**（損失HP係数×0.4は維持）。
* 瀕死ゲージ加速：HP38以下のゲージ増加を **+30%→+25%**（`addGauge` の倍率）。
* 属性攻撃コスト軽減（25%→15%）の発動閾値を **ゲージ70%→80%**へ（`atk25`判定・NOVAの`abSmartAct`分岐・UI文言を追従）。
* AUTOの属性攻撃率の期待レンジ（`ATTR_TARGETS`）を閾値変更に合わせて調整。

SHADOW（分身斬り／機動／AI）
* 分身斬り：相手がカウンター構えの場合、斬撃は **60%**に減衰して通し、カウンター返しは **分身が受けて本体ノーダメ**（返しは固有扱いの50%基準）。
* 命中時SPEED上昇：最終調整として **弱+10**（中+8／強+6、SPEED80以上は半減は維持）。
* 通常攻撃のゲージ加算のSHADOW上乗せは **採用せず**（他キャラと同じ基本値に戻す方針で整合）。
* AI：`accel` を薄くし、`unique`（分身斬り）寄せ＋カウンター過多を抑える方向で `abSmartAct` / `chooseCpuAction` を調整。

**同日（続き・AUTO分析／NOVA弱体・SHADOW案B・CPU）**
* NOVA：`hp` **95→90**、瀕死ゲージ増加 **×1.25→×1.2（+20%表記）**、瀕死・被ダメ軽減の閾値を **`hp <= floor(maxHp*0.4)`** に統一（`dealDamage` / `addGauge`）。`passiveDesc` 追従。
* **案B**：対NOVAの全キャラ共通メタ（`sealatk`/`guardbk` 上乗せ）を、**CPU/AUTO で SHADOW が相手 NOVA のときは適用しない**（`chooseCpuAction` NORMAL ブロック、`abSmartAct` 末尾ブロック）。SHADOW側の NOVA 専用追いは軽めに残す。
* SHADOW CPU：`weak` 偏重を緩め **`speedatk`/`mid`/`unique` 枝を増やす**（EASY／NORMAL／`abSmartAct`）。続けて **`opp.passive === 'learning'|'titan'|'blaze'` の粗い束（shPress）**でウェイト追加（短ターン圧対策）。
* `SYNTAX_WARS_GDD_v6.md` を **v6.17** に更新（上記＋案B＋shPress を変更ログに記載）。
* Downloads の `sw_auto`/`sw_detail`（4/21 付複数本）で再集計：**SHADOW 全体期待はまだ約40%**。対 NEX/TITAN/BLAZE は **shPress で +1〜2pp 程度**、NOVA 対面は案B＋NOVA弱体で **約30%→約32%** 付近まで改善。LYRA戦は試行ブレあり。

**同日（追加・バランス最終寄せ／SHADOW固有コスト／LYRA回復／BLAZE戦術AI）**
* SHADOW：CPUの `shPress`（learning/titan/blaze束ね）を廃止し、**まず NEXUS（learning）専用**の重みを追加（EASY／NORMAL／AUTO/HARD）。
* SHADOW：通常攻撃命中時のSPEED上昇（弱+10／中+8／強+6）で、**SPEED80以上でも半減しない**ように変更（上限100）。
* SHADOW：分身斬り（固有）のゲージコストを **50%→40%→35%**へ。UI表示・ボタン使用可否・AI・実消費を `uniqueGaugeCost()`/`canUseUnique()` で統一。
* LYRA-X：自動回復を **+3→+5→+4**に調整（ターン終了時、プレイヤー/CPU共通）。`passiveKeys`/`passiveDesc`/ログ表示も追従。
* BLAZE：AI戦術を「**起爆準備はゲージ50%まで我慢**→起爆後は**属性/固有を優先**＋**たまにguardでフェイント**」へ（CPU EASY/NORMAL、AUTO/HARD）。

### 触ったファイル（わかれば）

- `syntax_wars_v4html.html`
- `Syntax_Wars_作業ログ_貼り付け用.md`
- `handoff.md`
- `SYNTAX_WARS_GDD_v6.md`

### 次にやること（メモ程度でOK）

- **次エージェント／新セッション**：SHADOW CPU の **`shPress` をやめ、相手別に分岐**。着手は **NEXUS（`learning`）から**（学習済み・量子・属性の読みに合わせた重み）。同様に TITAN／BLAZE へ展開。
* NOVA閾値 **80%**固定で良いか（必要なら **75%**）と、NOVA **属性率がまだ低い**件は AUTO で継続確認。
* 新フォーマット `sw_detail` での集計は列増に合わせたスクリプトで。

### 気づいたこと・不具合

- 旧 `sw_detail` 前提の集計スクリプトは列構成が変わるため要更新。
* **コンテキスト肥大のため**このブロックまでを新チャットの前提にしてよい（本日の方針決定・数値は `handoff` / GDD v6.17 と一致）。

---

## 2026-04-20（AI/バランス調整：AUTOログ拡張・対NOVAメタ・CPU回復制限）

### 今日やったこと（ここに貼る）

AUTO BATTLE（`syntax_wars_v4html.html`）
* `📋 詳細` CSV を **全キャラ×全試合×全ターン**出力に拡張（従来は `abDetailLogs` の最初の5試合のみ記録）。

CPU AI（`syntax_wars_v4html.html`）
* **CPU回復制限**：全キャラ・全難易度で、CPUの `heal` を **TURN1〜2禁止／TURN3から許可**へ統一（`chooseCpuAction` / `abSmartAct` / `abRandomAct` / `abPrompt`）。

NOVA（分析→調整→再分析）
* AUTO出力（`sw_auto`/`sw_detail`）で、NOVAの **属性攻撃率が極端に低い**理由を分析（コスト軽減閾値到達頻度が低く、弱＋カウンターで試合が終わる）。
* 「NOVAの連打を見せる」目的で `abSmartAct` のNOVA挙動を調整（弱＋星屑砲で閾値到達→属性連打を出しやすく）。
* 調整後、NOVA勝率が極端に跳ねたため、**勝ち切りすぎを抑える方向へ部分的に戻し**（弱/固有/カウンターを緩め、ガードを少し混ぜる）。
* 追加調整（継続）：NOVAがまだ強すぎたため、以下を段階的に調整して再計測
  * 属性攻撃コスト軽減（NOVA）：発動閾値を **80%以上**（`atk25`）に寄せ、コスト下限を**15%**へ（10%は廃止）
  * 星屑砲（NOVA）：相手ゲージ吸収を**-30%（CROWD時-45%）**、使用後自ゲージ回復を**+10%**へ（テンポ破壊を緩和）
  * 瀕死補正（NOVA）：被ダメ軽減を**-20%**、瀕死ゲージ加速を**+25%**へ（過剰な逆転力を抑制）
  * 序盤補正（NOVA）：HP48以上の被ダメ軽減を**-5%**へ（-10%から弱体）

対NOVAメタ（全キャラ共通）
* 全キャラ共通で、相手がNOVAのとき **序盤の回復封じ（turn<=3）／回復直後の再封じ（opp.lastAction==='heal'）／星屑砲ライン（opp.gauge>=50）への圧（sealatk＋guardbk）**を追加。
  * CPU（`chooseCpuAction` のNORMAL側）と AUTO（`abSmartAct`）の両方に適用。

SHADOW / BLAZE（コンセプト強化＋AI調整）
* AIの方針を「mid/strong控えめ・weakで貯めて属性/固有へ」へ寄せ、キャラ別に微調整（強すぎ/弱すぎを見ながら再調整）
* SHADOW：通常攻撃の命中（ガード半減含む）でSPEED上昇（最終調整：**弱+10**／中+8／強+6、SPEED80以上は上昇量半分）に調整し、加速ゲージ依存を下げる方向に
* SHADOW：速度差回避（絶影）後、次の**中/強/カウンター**を「反撃必中」に（中/強は必中扱い、カウンターは成功確定）
* SHADOW：固有技を **影分身→分身斬り**に変更（ダメージ付き＋身代わり1回維持、影斬り相当×2.0）
* BLAZE：起爆準備（primed）中は **爆裂拳（固有）コスト0%を1回**許可し、固有技が実戦で回るように
* BLAZE：起爆準備中の被ダメ軽減を追加（**×0.8**）。さらに起爆準備後にカウンター反撃を受けた場合、反撃ダメージを **×0.7** に軽減
* LYRA：自動回復を**+3HP**、腐食を**-5HP（TITANは-3）**へ戻し、相性を緩めつつ個性を回復
* NEXUS：属性攻撃の固有バフを**×1.2**へ強化
* BLAZE：自爆ダメージを**10%**へ軽減

NOVA（星屑砲の弱体・回転率抑制）
* 星屑砲：基礎を **35** に調整しつつ、損失HP係数 **×0.4**、相手ゲージ **-20%（CROWD -30%）**、使用後自ゲージ回復を **+5%** に調整

AI（SHADOWの勝ち筋へ寄せ）
* SHADOW：固有技のウェイトを上げ、撃てる時（ゲージ50%以上）は固有をしっかり選ぶように調整
* SHADOW：弱攻撃の比率を上げてゲージ50%到達率を改善（CPU/AUTO側のウェイト調整）

### 触ったファイル（わかれば）

- `syntax_wars_v4html.html`
- `Syntax_Wars_作業ログ_貼り付け用.md`
- `handoff.md`
- `SYNTAX_WARS_GDD_v6.md`

### 次にやること（メモ程度でOK）

- 対NOVAメタ適用後の `sw_auto`/`sw_detail` で、NOVAの勝率が適正（45〜55%帯）に戻るか確認。
- 必要なら「キャラ別対NOVA戦術」へ発展（全キャラ共通上乗せは最小限に留める）。

### 気づいたこと・不具合

- （追記なし）

---

## 2026-04-20（GDD §8-3 難易度別UX・学習導線）

### 今日やったこと（ここに貼る）

設計・ドキュメント
* 対戦ナビ／リザルト／難易度の役割分担を **`SYNTAX_WARS_GDD_v6.md` §8-3** で固めたうえで、`syntax_wars_v4html.html` に実装。
* GDD を **v6.14** に更新（8-3 **実装反映済み**の記載）。`CLAUDE.md` の最新版表記も **v6.14** に合わせた。

実装（`syntax_wars_v4html.html`）
* **CPU・HARD**: 戦闘ヒント非表示。**CPU・NORMAL**: 盤面の事実のみ（SPEED差、封じ残り、SLOW、LYRA×腐食、CROWD 同時解決・ボーナス等）。**CPU・EASY**: 従来の CPU 説明に加え、選択コマンドに **【重さ】一行ラベル**（`ACTION_WEIGHT_NAV`）。
* **コマンド説明**: `ACTION_SUBTEXT` / `UNIQUE_SUBTEXT` に弱・中・強・ガードを追加し、全コマンドに **【重さ】** 行を付与（BLAZE ガード＝起爆準備に一言）。
* **リザルト（難易度共通）**: CROWD 要約を常時表示。**相手キャラ固有**は `passiveKeys` をキーワード行＋**タップで折りたたみ**（パッシブ／固有全文）。**2P** は P1/P2 それぞれ折りたたみ。
* **HARD**（CPU／オンライン）: 折りたたみ内に **カウンター成功回数・腐食被ダメ合計**（あなた／相手）。2P HARD は各プレイヤーブロック内に自分側のみ。
* **集計**: `matchStats`（`startGame` でリセット、`applyCounter` 成功時・腐食 -3HP 時に加算）。
* UI: `#battle-hint-wrap`、NORMAL 用 `#easy-hint.mode-normal`、リザルト用 `.result-extra` 等のスタイル追加。

引き継ぎ・作業ログ
* `handoff.md` / 本ファイル（作業ログ）に **本ブロックを追記**。

### 触ったファイル（わかれば）

- `syntax_wars_v4html.html`
- `SYNTAX_WARS_GDD_v6.md`
- `CLAUDE.md`
- `handoff.md`
- `Syntax_Wars_作業ログ_貼り付け用.md`

### 次にやること（メモ程度でOK）

- NORMAL ナビの密度・文言はプレイ感で微調整。オンラインでもナビを出すかは要判断（現状は CPU のみ）。

### 気づいたこと・不具合

- （追記なし）

---

## 2026-04-20（HUD / CROWD表示 / 仕様追従）

### 今日やったこと（ここに貼る）

UI（`syntax_wars_v4html.html`）
* EASYヒント（`#easy-hint`）を **READY直下**へ移動（旧：ボタン最下部）。`HINT` トグル位置も追随。
* CROWD告知（`#battle-context-strip`）を **キャラパネル直下（AUDIENCE直上）**へ移動。
* `crowdOnFireNext` 中は **AUDIENCE枠を非表示**にして、CROWD帯だけを同じ縦スロットに表示（ゲージ0固定の冗長表示を解消）。
* 文言：`⚡ 次ターン両者同時行動` に統一。CROWD関連ログも「同時解決」表現から寄せた。

仕様・GDD
* `SYNTAX_WARS_GDD_v6.md` を **v6.12** に更新：**ジャストガード成立時は強攻撃の `SPEED-10` を付与しない**（強×ガードは常にジャスト扱いのため実質ジャスト時のみ不発）。

バグ修正（`syntax_wars_v4html.html`）
* 強攻撃の `SPEED-10` が **`dealDamage()` より前に適用**され、ジャスト後も降速してしまう不具合を修正（最終ダメージが0なら不発）。

運用メモ
* `CLAUDE.md` に「変更前にユーザー確認」「自然言語での合意を先に」ルールを追記。

### 触ったファイル（わかれば）

- `syntax_wars_v4html.html`
- `SYNTAX_WARS_GDD_v6.md`
- `CLAUDE.md`

### 次にやること（メモ程度でOK）

- 実機で CROWD 中の入れ替え表示（AUDIENCE非表示）と、EASYヒント位置の体感を確認。

### 気づいたこと・不具合

- （追記なし）

---

## 2026-04-19（セッション2・新チャット引き継ぎ）

### 今日やったこと（ここに貼る）

仕様・GDD
* `SYNTAX_WARS_GDD_v6.md` を **v6.11** に更新。前ターンに**双方オーバークロック**した次ターン（双方2回行動ターン）では、ガード時の**ジャストガード枠を各自2回**まで（強ヒットのジャストごとに1消費。弱・中の半減は消費しない）。絶影／CROWD 由来の2回行動は対象外。
実装（`syntax_wars_v4html.html`）
* `doubleFromOverclock`（OC起因の2回行動フラグ）と `guardJustCharges` を追加。`serializeState` / `applySerializedState` 対応。
* OC使用時に `doubleFromOverclock = true`。CROWD で SHADOW が `pendingDouble` を**新規**付与するときだけ `doubleFromOverclock = false`（既に OC で `pendingDouble` がある場合は OC フラグを潰さない）。
* `resolveActions` 末尾で「その解決で2回行動だった側」のみ `doubleFromOverclock` をオフ（`endOfTurn` では消さない。次ターンの双方OC判定用）。
* 絶影ログ付きの `addAudience` 分岐も同様に `wasPending` ガード。
スマホ・選択画面
* キャッチコピーを短く戦略寄りにリライト（`CHARS.catchphrase`）。
* `.char-catchphrase` に `word-break: keep-all` / `overflow-wrap: anywhere` 等で不自然改行を緩和。
* `.fighter-name` に ellipsis・`min-width:0` でバトルHUDの名前欠けを緩和。
ログ（2回目が見えない問題）
* `last-log-bar` の保持行数を **3 → 6**（高密度ターン向け）。
* オーバークロック／絶影の**追加解決**の直前に `▶ 2回目 名前: 技名`（`log-action`）を追加。
* SPEED差70の追撃の直前に `▶ 追撃 …` を追加。
キャラ選択（キャッチフレーズ）
* 6キャラの `CHARS.catchphrase` を再整理（句点なし・2行前提）。
  * NEXUS: `被弾で解析　同属性で破砕`
  * SHADOW: `超速で回避　反撃で処理`
  * NOVA QUEEN: `溜めて連打　臨界で撃ち抜く`
  * TITAN: `遅いほど強い　押して潰す`
  * LYRA-X: `slowで腐食　持久で削れ`
  * BLAZE: `起爆して煽れ　ドカンと決めろ`
* キャッチフレーズは全角スペース（`　`）を `<br>` に置換して、選択画面で**必ず2行**表示に。
演出（アクションPOP）
* キャラ上に出る POP（JUST GUARD / GUARD BREAK / COUNTER / MISREAD）の文字サイズを拡大、表示時間を延長して視認性を改善。
EASY（VS CPU）体験強化
* CPUが「完全ランダム」ではなく、**キャラの持ち味＋短いフェーズ（3ターン巡回）**で行動分布が出るように `chooseCpuAction()` を調整（相手状況は見ず、自分状況だけ参照）。
* `#easy-hint` を拡張し、EASY時に以下を表示するように改善（視認性のため背景/枠/影も追加）
  * `CPU特徴:`（キャラ固定の説明）
  * `CPU傾向:`（履歴の要約ラベル＋直近4手の行動列）
  * 選択中ボタンの説明（従来のサブテキスト、HINT:OFFで非表示）
* `actionHistory` の保持数を EASY（VS CPU）時のみ **5 → 9** に増やし、傾向ラベルのブレを抑制。
* CPUの「回復」は **TURN 1 では選ばない**ように制限（CPU側のみ。プレイヤーは回復可能のまま）。

### 触ったファイル（わかれば）

- `syntax_wars_v4html.html`
- `SYNTAX_WARS_GDD_v6.md`

### 次にやること（メモ程度でOK）

- Netlify 等へ **V4 をデプロイ**したら、iPhone でログ6行・2回目行・OC同士ガード2ジャストを実機確認。
- `handoff.md` の「次セッション」タスク（音声・HUD 等）を継続。

### 気づいたこと・不具合

- 旧 `last-log-bar` 3行だと、1ターン目のログで2回目の解決ログが押し流されやすかった。

---

## 2026-04-19（テンプレ：この見出しの日付を変えて使う）

### 今日やったこと（ここに貼る）

チュートリアル
* STEPSを10ステップから6ステップに圧縮。STEP1からCPUが強攻撃してくる構成に変更し「ガードして読み勝つ」快感を最初に持ってくる
* チュートリアルパネルをfixed下部オーバーレイからログエリアと同じフローレイアウトに移動。ボタンが押せない問題を根本から解消
* パネルのメッセージを18px太字に拡大、オレンジのヒント行（▶ ガードを選んでREADY）を追加
* 最終ステップの締め文を「あとは実戦で確かめろ。」に変更
UI レイアウト刷新
* 左ログ・右ボタンの2カラムを廃止
* ボタンを全幅4列に変更
* ターン情報＋直前ログをバナー1行エリアに統合
* EASYヒント用の #easy-hint をボタン下に追加（今は空）
ログ
* battle-log DOM廃止 → last-log-bar に直近3行を表示する方式に変更
* ターンごとに resolveActions 冒頭でクリアして毎ターン新鮮な状態に
* リザルトの📜LOGボタンは actionLogBuffer から全履歴を生成する形に変更
演出（フラッシュ）
* JUST GUARD / GUARD BREAK / COUNTER / MISS を画面中央に0.5秒大きく表示
* カウンターは300msのタメあり
* ガード破壊は相手がガード中の時だけ演出が出るよう修正
HUD
* バーの高さ・ラベル・数字を全体的に大きく
* 難易度別情報開示を実装：EASY/NORMALは数字表示、HARDは25%刻み目盛りのみ、SPEEDは難易度問わず常に表示
ログスタイル
* JUST GUARD・GUARD BREAK → シアン太字
* COUNTER成功 → オレンジ太字
* MISS・空振り → グレー斜体
今日（4/19）のプロジェクト作業をまとめます。


⚔️ バトル画面のログ改善（このセッション）
① バトル中ログの視認性改善
* 「SELECT ACTION」の文字色を #506080（暗い）→ #e0f4ff（明るい白）＋光彩追加
* ログのデフォルト行色を #607080 → #b0c8e0（読みやすい明るさ）に変更
* 行動サマリー（▶ あなた: 弱攻撃 / CPU: ガード）に log-action クラスを新設 → #e8f0ff・太字で目立つように
* NORMAL / SPECIAL ATTACK などのカテゴリラベルを #304060（ほぼ見えない）→ #70a8d0（明るい青・太字）
* アクションボタンの文字色もやや明るく調整
② バトル後ログ（📜LOG画面）の視認性改善
* デフォルト行色を #8090b0 → #b0c8e0 に変更
* バトル中ログと完全に同じカラー体系を適用
    * 行動サマリー → 白・太字
    * ダメージ → 赤・太字
    * カウンター → オレンジ
    * 回復 → 緑
    * ステータス → 黄
    * ターン区切り → 青斜体


お疲れさまでした！今日の作業まとめます。

今日の作業まとめ（2026/4/19）
🎯 アイデア・仕様決定
ポップラベルの設計
* 表示条件をクラウドゲージが溜まる条件と一致させる方針に決定
* JUST GUARD ✨ / GUARD BREAK 💥 / COUNTER ⚡ → 評価ラベル
* MISREAD → 食らった側に表示（完全対称設計）
* DEFENDED → ポップではなくログの説明テキストに降格
* 相手がガードしていない時のガード貫通はポップなし
コマンド名変更
* 「ガード破壊」→「ガード貫通」に変更（名前と効果のズレを解消）
コマンドサブテキスト
* 選んだ列以外の空きスペースに表示する方式に決定
* EASYモードのみ表示・ON/OFFトグル付き
キャラ固有チュートリアルポップ
* 初回選択時のみキャラの戦術ヒントをポップ表示

🖥️ キャラ選択画面の改善
* 円形キャラ画像をカード左側に追加（戦闘画面の処理を流用）
* キャッチコピーをホワイト系で追加
* ステータス数値はシアンのまま維持
* PASSIVEパネルをキーワード行＋SPECIAL MOVE 1行のみに簡略化
* FIGHTボタンを画面最下部に固定（キャラ追加にも対応）

🐛 バグ修正確認
* 相手がガードしていない時に「ガード破壊成功」と表示されるバグを特定
* 修正方針：相手がガード中の時のみ GUARD BREAK ログを出す

⏱️ 演出タイミング修正
* ポップ表示時間が短すぎる問題を確認
* JUST GUARD / GUARD BREAK / COUNTER → 1.2秒
* MISREAD → 0.8秒
* DEFENDED → 0.6秒
* フェードアウト → 0.3秒

📋 戦闘中テキスト修正
* オレンジの説明文をfont-weight 700〜800・font-size+2〜4pxに拡大

📝 作成した指示書
1. SYNTAX WARS 改修指示書 v1（全項目まとめ）
2. PASSIVEパネル簡略化プロンプト
3. 演出タイミング＋テキスト修正プロンプト

次回やること候補としては結果ポップの実装と外部公開の準備が残ってるで！



### 触ったファイル（わかれば）

-`syntax_wars_v4html.html`


### 次にやること（メモ程度でOK）

- 


### 気づいたこと・不具合

- 


---

## コピー用：次の日のブロック

新しい日を始めるとき、下からここまでコピーして、上の区切り（`---`）の**直後**に貼る。

```markdown
## YYYY-MM-DD

### 今日やったこと（ここに貼る）



### 触ったファイル（わかれば）

- 


### 次にやること（メモ程度でOK）

- 


### 気づいたこと・不具合

- 


```

---

※ 長い会話の代わりに、このファイルを `@` すると続きの作業がしやすい。
