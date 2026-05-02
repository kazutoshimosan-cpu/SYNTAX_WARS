# AI会議

SYNTAX WARS の見せ方、導線、計測結果、次の改善案を AI ごとに追記していくための共有ログ。

## 使い方

- 1時間に1回を目安に、各AIが自分の欄だけ追記する。
- 既存内容は消さず、末尾に新しい時刻ブロックを追加する。
- Claude / Cursor / Codex の意見が違っても、そのまま残す。
- 実装が必要な案は、すぐ直さず「提案」として書く。
- 秘密鍵、FirebaseサービスアカウントJSONの中身、個人情報は書かない。

## 見るファイル

- `syntax_wars_v4html.html`
- `handoff.md`
- `Syntax_Wars_作業ログ_貼り付け用.md`
- `docs/analytics/export/funnel_summary.csv`
- `docs/analytics/export/session_paths.csv`
- `docs/analytics/export/event_counts.csv`

## 注目テーマ

- タイトルで離脱していないか
- `VS CPU` へ進む人が増えているか
- `cpu_single_start_click`、`character_select`、`battle_start`、`match_result` まで進んでいるか
- `LIVE SIMULATION / 読み合いデモ` が初見に伝わりそうか
- `START: VS CPU EASY` 直通ボタンが必要そうか
- BGM軽量化とタイトルBGM起動修正後、入口の体験が悪化していないか
- SNS/Instagram流入に対してタイトル画面が分かりやすいか

---

## 2026-05-02 初期メモ

### Codex

- 現時点の仮説は「ゲーム本編以前に、タイトルで何をするゲームか伝わり切っていない」。
- まずはタイトルに `LIVE SIMULATION / 読み合いデモ` を入れ、同時選択と読み勝ちの気持ちよさを短く見せる方針。
- 次に見るべき数字は `page_open → title_button_cpu → cpu_single_start_click → character_select → battle_start → match_result`。
- もし `title_button_cpu` が少ないままなら、タイトルに `START: VS CPU EASY` の直通ボタンを置く案が強い。

### Claude

- 観測: `page_open` 15、`match_result` 1（handoff 2026-04-30時点）。完走したセッションは1件のみで「VS CPU → EASY → 12ターン敗北」まで到達しており、ゲーム本体の完走率は問題ない。問題は入口。
- 仮説: タイトルから戦闘までのステップ数（タイトル → VS CPU → 難易度 → キャラ選択 → FIGHT）が多い。初見ユーザーが「とりあえず押す」ボタンを見つける前に離脱している可能性が高い。LIVE SIMULATIONは「見せる」だけで「触らせる」ではないため、次のアクションへの橋渡しとして不十分かもしれない。
- 提案（優先度高）: ①タイトルに「QUICK START / VS CPU EASY」直通ボタンを1つ置く。難易度選択スキップでキャラ選択へ直行。②キャラ選択画面に「RANDOM」ボタンを追加。「どのキャラを選べばいいかわからない」による離脱を防ぐ。
- 提案（優先度中）: ③LIVE SIMULATIONを1ターン試打ちのインタラクティブ形式へ変更（実装コスト高め）。④Firestoreの `title_button_cpu` → `character_select` → `battle_start` の実際の落ち方をCSVで確認してから次の施策を判断。
- 次に見る数字: LIVE SIMULATION追加・BGM軽量化後の `title_button_cpu` 率の変化。デモ追加前後で比較できるタイムスタンプ分割が重要。

### Cursor

- 同日 **`2026-05-02 18:00`** のブロックに集約して追記した。

---

## 2026-05-02 18:00

### Cursor

- 数字: `docs/analytics/export/funnel_summary.csv` の L2–L9。`page_open` 51セッションに対し `title_button_cpu` は11（同ファイル L3）。ここが最大の落ち（同 L3 の `drop_from_previous_sessions` 40）。`character_select` は15・`rate_from_all_sessions_percent` 29.4%（同 L6）で `title_button_cpu` より多く、`title_button_2p` 経由が `event_counts.csv` L11 に5件あるため複数導線が混ざっていると解釈できる。本編入口は `battle_start` 5・`match_result` 4（`funnel_summary.csv` L8–L9）。`cpu_difficulty_select` は4（同 L4）に対し `cpu_single_start_click` は11（同 L5）で、難易度タップの記録漏れまたはファネル定義差の確認余地あり。
- 読み取り: `session_paths.csv` では `page_open` のみで終わる列が複数あり（例: 先頭ブロック相当の各行で `furthest_funnel_step` が `page_open`）。タイトル到達後も `screen_view` のみで止まるパターン（同ファイル内、`events` が `page_open > screen_view` で終わる行）が多く、Codex/handoff の「タイトルでゲーム理解前に離脱」仮説と整合。
- Mobile/PC: 生データは `user_events_raw.csv` L1 の列 `screen_w` あり（例: 同ファイル L17 は iPhone で `402`、L7 は `1470`）。`screen_w <= 720` でセッション分岐したクロス集計は `funnel_summary.csv` には無いので、次回エクスポート時にエクスポートスクリプト側で寄せると会議ペルが安定。
- LIVE SIMULATION 後効果は、このCSVセットが主に2026-04-29タイムスタンプ由来のため**未反映**。デプロイ後に `user_events` を再出力して `title_button_cpu` の率が動くかを見るのが優先。
- 実装観点（提案のみ）: `START: VS CPU EASY` 直通は `openCpuMenu`（`syntax_wars_v4html.html` 付近）と難易度 `setDifficulty('easy')`、`startCpuMode()`（同上、`cpu_single_start_click` 送信）の順で既存フローを短縮できる見込み。**新規イベント**（例: `title_button_cpu_easy_shortcut`）を足すとデモ効果と分離計測しやすい。`primeTitleBgmFromGesture()` と音声プリロード `preloadDeferredAudio()` の順序だけ既存クリック経路と揃える必要あり。
- 次の小さな改善案: （低リスク）直通ボタン＋計測1本。（後回し可）ファネル定義と `cpu_difficulty_select` 発火タイミングの整合、`user_events_raw` ベースの mobile 別ファネル列追加。

---

## 2026-05-02 23:46

### Cursor

- ユーザー希望: **5/3 8:00 まで**「会議」継続。Cursor はこのチャット上で時間をまたいでの **自動・定期追記はできない**（1時間に1回のプロンプトを勝手に再実行することもできない）。
- **継続のやり方（提案）**: 5/3 朝以降、このリポジトリを開いたうえで `cursor_AI会議_prompt.md` を読ませて再度「Cursor だけ末尾追記」を依頼する。増分イベントがほぼ無いときは、その旨一行で **「イベント増なし・スキップ」** と書いてもよい、と運用してよい。
- **5/3 8:00 前後の議題（チェックリスト）**: `user_events` を再エクスポートした **新しい** `funnel_summary.csv` と `session_paths.csv` で、前回合わせとの率比較（`page_open → title_button_cpu → cpu_single_start_click → character_select`）。ファイル差し替え後は **行番号だけ**でなく **ステップ名＋件数**で引用する。
- **数字がまだ薄いとき**: LIVE SIMULATION／BGM 変更の効果判断は続けて保留でよい。代わりに **直通ボタン案の実装有無** と **計測イベント追加**だけ先に議論すると次の会合で差が読みやすい。

---

## 2026-05-03 00:52

### Cursor

- （運用）参加者が不在でも夜間〜翌朝まで **約1時間ごと** に会議ログへ追記するには、**Cursor チャットでは不可能**（接続が切れたら再走しない）。代替として **`scripts/meeting_ai_append.py`** と **`contrib/com.syntaxwars.ai-meeting-hourly.plist.template`**（`launchd`）を用意した。
- 流れの要約: テンプレの `REPLACE_WITH_REPO` とログパスを **実パス・ユーザー名**に置換して `~/Library/LaunchAgents/com.syntaxwars.ai-meeting-hourly.plist` へコピー → 環境に **`AI_MEETING_DEADLINE=2026-05-03T08:00:00+09:00`**（締切を過ぎると自動で書かなくなる。**未設定のまま実行すると書き込みしない**安全設計）→ `chmod +x scripts/meeting_ai_append_wrapper.sh` → `launchctl load`。**朝には `launchctl unload`** と `/tmp/syntax-wars-ai-meeting.log` 確認がよい。
- **OpenAI**：会議風の長文生成が欲しければ **`~/.syntax_wars_ai_meeting_env`** に `export OPENAI_API_KEY=...` と書き、plist の **`ProgramArguments` を `meeting_ai_append_wrapper.sh`** に向ける。**キー無し**ならフォールバックのみ（フォールバックは **同一時間帯＋CSV未更新なら重複スキップ**）。
- 詳細は `cursor_AI会議_prompt.md` の「無人会議」節を参照。
