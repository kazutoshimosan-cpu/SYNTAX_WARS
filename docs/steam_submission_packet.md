# SYNTAX WARS — Steam「近日登場」入稿パッケージ（貼るだけ版）

> 作成 2026-06-23。**目的＝帰宅後の Mac 作業を「考える」から「貼る・上げる」だけにする。**
> 上から順に、Steamworks の各フィールドへ下の値をコピペ／ファイルをアップするだけ。
> コピー正本＝`steam_store_page.md`（こちらは入稿用に整形＋About を **BBCode 変換**済）。
> 素材正本＝`~/Desktop/SYNTAX_WARS素材/`（`steam_capsules/` ＋ `steam_screenshots/`）。

---

## 0. はじめに（Mac で最初にやること＝登録の前段）

入稿フォームに入る前に、Steamworks 側でアプリの「箱」を用意する。

1. **Steamworks パートナーアカウント**（無ければ作成）＝ <https://partner.steamgames.com/>
2. **Steam Direct 手数料 $100/タイトル** を支払う（売上 $1,000 到達で還元される運用）。
3. **本人確認・税務・銀行情報**（Paxum/銀行）＝米国税務 W-8BEN 等。ここが審査の実体。
4. アプリ作成 → **App ID 発番**。以降このパケットのフィールドを埋める。
5. ★**ストアページは発売の最低2週間前から公開**が Steam 推奨（ウィッシュリストを溜める箱）。だから「近日登場」を先に立てるのが正解＝[[project-platform-positioning]] の方針どおり。

> 注：Steam の細目（小カプセル要求px・オンボーディング手順）は更新されることがある。**フォームに表示される要求サイズ／必須項目を正として、本書とズレたらフォーム優先**。

---

## 1. 基本情報（Basic Info）

| フィールド | 入れる値 |
|---|---|
| **App name** | `SYNTAX WARS` |
| **Release date** | **Coming soon**（日付未定でOK＝価格・時期は発売直前に実データで確定） |
| **Developer** | （ユーザーのスタジオ名／個人名） |
| **Publisher** | （同上・自己パブリッシュ） |
| **Primary genre** | `Strategy` |
| **Secondary genre** | `Indie` |
| **App type** | Game |

---

## 2. 対応言語（Supported Languages）★実態に合わせる

| 言語 | Interface | Full Audio | Subtitles |
|---|---|---|---|
| **日本語 / Japanese** | ✅ | — (音声なし) | ✅ |
| **English** | ✅ | — (音声なし) | ✅ |

> ★**ゲーム内 英語化＝完成（2026-06-24・Godot push済 ..`1f8829a`）**＝OPTIONSの言語トグルで全画面 JA⇄EN（タイトル/選択/戦闘/あそびかた/チュートリアル/OP/ED/勝利台詞/実績）。よって **English の Interface ＋ Subtitles を申告してよい**（虚偽でない）。音声は元々なし＝Full Audio は両言語とも「—」。
> **申告前の最終確認（推奨・帰宅後Mac）**：実機で English に切替えてレイアウト目視。※固定幅UIのオーバーフローは**ヘッドレス実寸監査で機械的にクリア済**（強技名→compactティア `(S)/(W)`・アーケード主ボタン整形）＝残りは最終目視のみ。
> **既知の軽微な残JP（申告に影響しない）**：バトルログ復習欄（Lキーで開く隠し二次機能・JS由来の戦闘ログ）のみ日本語。プレイヤー導線・物語は全EN＝「English UI対応」の表記は妥当。
> ストアページの英語コピーは従来どおり別管理（`steam_store_page.md` EN）。

---

## 3. タグ（Store Tags）— 確定12個

```
Turn-Based   Strategy   PvP   1v1   Psychological   Tactical
Cyberpunk    2D   Local Multiplayer   Singleplayer   Indie   Sci-fi
```

> ★**`Fighting` は入れない／`Card Game` も入れない**（格ゲー・デッキ構築の誤集客回避＝ポジショニングの核）。「カード寄り」の手触りは下の本文（Inscryption 参照）で拾う。

---

## 4. 短い説明（Short Description）★300字以内・貼るだけ

**JP（136字 / 上限300）**
```
6体のサイバーファイターによる、同時開示の一騎打ち。手を伏せて選び、せーので開く——反射神経じゃない、読み合いだ。強・弱・ガードの三すくみを、相手と同時に選んで同時に開く。Inscryption のブラフ、Footsies の間合い読みが好きなら刺さる、一手の重い心理戦。
```

**EN（287字 / 上限300）**
```
A cyberpunk duel of pure reads. Both fighters lock in a move in secret, then flip at the same instant — no reflexes, no combos, just mind games. Strong eats Weak, Weak pierces Guard, Guard parries Strong. Loved the bluffing in Inscryption or the spacing in Footsies? Step into the arena.
```

---

## 5. About This Game（フル説明）★Steam BBCode 変換済・そのまま貼る

> ⚠️ **元コピーは Markdown。Steam ストアは BBCode。** 下は BBCode に変換済＝`###` や `-` をそのまま貼ると崩れるのを回避済み。Steamworks の説明欄に**この通り**貼る。

### 5-JP（日本語ストア説明欄に貼る）

```bbcode
[h2]◆ 殴り合いは、もう十分見た。[/h2]
SYNTAX WARS は、[b]手を伏せて選び、同じ瞬間に開く[/b]サイバーパンクの一騎打ち。反射神経も、コンボ表もいらない。要るのは——相手を読み切る一手だけ。

[h2]◆ 同時開示。それが全て。[/h2]
あなたと相手は、毎ターン同時に手を選ぶ。せーので、開く。
[list]
[*][b]強[/b]は弱を飲み込む／[b]弱[/b]はガードを貫く／[b]ガード[/b]は強を弾く。
[*]最強の手は存在しない。あるのは「相手は何を選ぶか」だけ。
[/list]
シンプルな三すくみが、開示の一瞬で深い読み合いになる。

[h2]◆ 反射じゃない、読みだ。[/h2]
Slay the Spire の駆け引き、Inscryption のブラフ、Footsies の間合い読みが好きなら——ここがあなたの闘技場。1試合は短く、1手は重い。負けたら「次は読むぞ」と即リスタートしたくなる。

[h2]◆ 6体の戦士、6つの読み筋。[/h2]
[list]
[*][b]NEXUS-7[/b] — THE ELITE ENFORCER｜学習するアンドロイド。戦うほど強くなる万能型。
[*][b]SHADOW[/b] — THE PHANTOM ASSASSIN｜最速のアサシン。強気の攻めでねじ伏せる。
[*][b]TITAN[/b] — THE IRON JUGGERNAUT｜最も鈍重で最も硬い。重圧でじわじわ削る。
[*][b]NOVA QUEEN[/b] — THE QUANTUM EMPRESS｜追い詰められるほど凶悪に。逆転の女帝。
[*][b]LYRA-X[/b] — THE BIO-ABERRATION｜腐食で削り、再生で耐える。粘りの異形。
[*][b]BLAZE[/b] — THE DETONATOR｜溜めて、爆ぜる。読み外しを一撃で帳消しにする狂気。
[/list]

[h2]◆ モード[/h2]
[list]
[*][b]VS CPU[/b]（難易度あり／"読む"AI）
[*][b]アーケード5連戦＋しばりチャレンジ[/b]（ノーガード／固有技なし／全キャラ制覇の実績）
[*][b]ローカル対戦（2P）[/b]
[/list]

[h2]◆ その奥に、静かな物語[/h2]
封印された最強AI「ARCHITECT」を賭けた、戦争の代わりの興行トーナメント。勝ち進むほど、ある戦士の中で"何か"が還ってくる……（ロアは語りすぎず、勝てば滲む）。

[h2]◆ 買い切り。広告なし。[/h2]
基本無料の収奪なし。一度買えば、全部。
```

### 5-EN（English ストア説明欄に貼る）

```bbcode
[h2]◆ Enough button-mashing.[/h2]
SYNTAX WARS is a cyberpunk duel where you [b]commit your move in secret and reveal it at the very same instant[/b]. No reflexes. No combo tables. Just one thing — out-read your opponent.

[h2]◆ The reveal is the whole game.[/h2]
Every turn, both fighters lock in at the same time — then flip together.
[list]
[*][b]Strong[/b] eats Weak. [b]Weak[/b] pierces Guard. [b]Guard[/b] parries Strong.
[*]There's no best move. There's only "what will they pick?"
[/list]
One simple triangle turns vicious the instant the moves flip.

[h2]◆ Reads, not reflexes.[/h2]
If you live for the bluffs in Inscryption, the mind games of Slay the Spire, or the spacing in Footsies — this is your arena. Matches are quick. Every choice is heavy. And every loss ends the same way: "one more — this time I read it."

[h2]◆ Six fighters. Six ways to read.[/h2]
[list]
[*][b]NEXUS-7 — The Elite Enforcer.[/b] A learning android that grows stronger the longer it fights. Your all-rounder.
[*][b]SHADOW — The Phantom Assassin.[/b] The fastest blade on the floor. Pure aggression, no mercy.
[*][b]TITAN — The Iron Juggernaut.[/b] The slowest, the toughest. Grinds you down under sheer pressure.
[*][b]NOVA QUEEN — The Quantum Empress.[/b] The closer to death, the deadlier. The queen of comebacks.
[*][b]LYRA-X — The Bio-Aberration.[/b] Corrodes to chip you, regenerates to outlast you.
[*][b]BLAZE — The Detonator.[/b] Charge up, then blow up. One hit erases a bad read. Pure chaos.
[/list]

[h2]◆ Modes[/h2]
[list]
[*][b]VS CPU[/b] — difficulty options, and an AI that actually reads you.
[*][b]Arcade Gauntlet (best of five) + Challenge Runs[/b] — No Guard, No Signature, clear with all six. Earn the achievements.
[*][b]Local 2-player[/b] — hand a friend the other controller.
[/list]

[h2]◆ A quiet story underneath.[/h2]
A sealed superintelligence called ARCHITECT is the prize — fought over not with armies, but with a public tournament. Win, and something begins to... return, inside one of the fighters. The lore never lectures. It bleeds through when you win.

[h2]◆ Buy once. No ads. No traps.[/h2]
One purchase. Everything included. No free-to-play hooks, ever.
```

---

## 6. グラフィック素材（どのスロットに どのファイル）

すべて `~/Desktop/SYNTAX_WARS素材/steam_capsules/` 内。**実寸は全て Steam 要求ピッタリで測定済**。

| Steam スロット | 要求px | 上げるファイル | 備考 |
|---|---|---|---|
| **Header capsule** | 460×215 | `header_460x215.png`（英語ストア時 `_en`） | タグライン焼込みのEN版あり |
| **Small capsule** | 462×174 ※ | `small_462x174.png` | ロゴのみ＝言語中立・EN版不要 |
| **Main capsule** | 616×353 | `main_616x353.png`（／`_en`） | 検索/おすすめ枠の顔 |
| **Vertical capsule** | 374×448 | `vertical_374x448.png` | ロゴのみ＝EN版不要 |
| **Library capsule** | 600×900 | `library_600x900.png`（／`_en`） | ライブラリ/WL縦サムネ |
| **Page background** | 1438×810 | `page_background_1438x810.png` ✅ | op_p4を暗め加工・作成済（任意枠だが用意） |
| **Library Hero** | 3840×1240 | `library_hero_3840x1240.png` ✅ | 発売時用・中央シャープ＋左右ボカし延長 |
| **Library Logo** | 透過PNG | `logo_transparent.png` ✅ | 既存・★ロゴ位置は「bottom」系に（顔の上書き回避） |

> ※ **Small capsule のpx**：長く 231×87 だったが高解像度化で 462×174 を受け付ける。**アップロード時にフォーム表示の要求サイズと一致するか一目確認**（462×174 でいけるはず／ダメなら 231×87 に縮小版を出す）。
> **ロゴ素材** `logo_transparent.png`（1431×229・透過）＝ライブラリHero/Logo を作る時の素材（発売時・§9）。各キャプセルの言語版は **JP=無印／EN=`_en`**。英語ストアページには `_en`、日本語ストアページには無印を割り当てる。

---

## 7. スクリーンショット（順番どおりに5枚以上アップ＝7枚ある）

すべて `~/Desktop/SYNTAX_WARS素材/steam_screenshots/`・全 1920×1080。**この順番**で（1枚目＝タグライン体現の"伏せ札"）：

1. `01_伏せ札_commit.png` — 伏せ札 ？vs？（タグライン「伏せた手が」を体現＝先頭の顔）
2. `02_同時開示_reveal.png` — せーので開く瞬間
3. `03_ジャスガ_DEFLECT.png` — ガードで弾く＝守りの読み
4. `04_量子ビーム_NEXUS.png` — 署名級FX（量子ビーム）
5. `05_分身斬り_SHADOW.png` — 署名級FX（分身斬り）
6. `06_CROWD_ON_FIRE.png` — 観衆が沸く見世物の瞬間
7. `07_YOU_WIN.png` — 決着（KO/YOU WIN）

> UIは現状JP。EN対応時に差し替え予定（今はJPのままで出してOK＝ストアの英語コピーで世界観は伝わる）。
> （任意・後追い）カウンターの読み合いカットは現素材に未収録＝帰宅後に実機で1カット録れば守りの厚みが増す。

---

## 8. トレーラー / 動画

| スロット | ファイル | 備考 |
|---|---|---|
| **Store trailer（16:9）** | `~/Desktop/SYNTAX_WARS素材/トレーラー_v1_BGM版.mp4` | WL効果大・任意だが強く推奨。**最初に再生される顔** |

> ★**9:16縦版（`トレーラー_縦9x16_字幕下.mp4`）はSteamには上げない**＝SNSフィード(TikTok/Reels/Shorts)専用。Steamは16:9。
> Steam は動画を **MP4 で受ける**（H.264/AAC）。審査で別途トランスコードされる。

---

## 9. 残ギャップ＆チェックリスト

**今すぐ出すのに「必須でない」もの（後追いOK）：**
- ✅ **ページ背景 1438×810** ＝ `page_background_1438x810.png`（作成済・op_p4暗め）。
- ✅ **ライブラリ Hero 3840×1240** ＝ `library_hero_3840x1240.png`（作成済）／**Library Logo** ＝ `logo_transparent.png`（既存）。**発売時はこの2つをそのまま使える**（★ロゴ位置は「bottom」系に設定＝顔の上書き回避）。
- ◻️ **カウンター読み合いのスクショ1枚**＝任意・帰宅後に実機で。
- ◻️ **ライブラリ Header 920×430**（任意・一部表示枠）＝必要なら Hero から切り出せる。

**発売までに確定するもの（Coming Soon では未定でOK）：**
- 価格（仮 $8.99／発売週−15% で実売≈$7.6・[[project-launch-scope]]）
- リリース時期
- **英語UI（in-game i18n）＝✅完成済（2026-06-24・Godot ..`1f8829a`）**＝§2 の言語欄に English（Interface＋Subtitles）を申告済。残＝実機での最終レイアウト目視のみ（オーバーフローは監査でクリア済）

**Mac で踏む順（まとめ）：**
1. Steamworks 登録 → Steam Direct $100 → 本人/税務/銀行 → App ID 発番
2. §1〜§8 をこのパケットから貼る・上げる
3. コンテンツ調査（暴力表現等のアンケート）に回答＝サイバー戦士の対戦・流血表現は控えめ
4. **「Submit for review」** → Valve 審査（おおむね 1〜数営業日）
5. 公開 → ウィッシュリスト受付開始 → SNS の CTA を「ウィッシュリスト受付中」に差し替え

---

## 付録：このパケットと正本の対応
- コピー正本＝`steam_store_page.md`（§2 タグライン／§3 短文／§4・4b About／§6 タグ）
- 本書は §4/4b の About を **BBCode 変換**し、Steamworks のフィールド順に並べ替えたもの。
- 文言を直すときは **`steam_store_page.md` を正本として直し → 本書の BBCode を再変換**（二重メンテ回避）。
