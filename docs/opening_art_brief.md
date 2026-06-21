# SYNTAX WARS — OP作画指示書（Art Brief）

> 台本＝[`opening_panels.md`](opening_panels.md)。本書は**外部作画→投入**用の発注仕様。
> 投入フロー＝ED方式（画を描く→`assets/art/opening/` に置く→Godotでimport→枠が実画に切替）。
> **★2026-06-21 3部門レビュー反映版**（creative/narrative/art の指摘を全反映）。

> ## ★発注優先度（重要）
> **先に上げる＝P1／P4／P5（OP入口3枚＝発売ブロッカーP0）**。初回起動の没入の入口なので枠卒業が最優先。
> しかも **P4(伏せ)→P5(同時開示) はそのままストア主役GIF「伏せ→開示→解決」の中身**＝この2枚でP0が2つ同時に進む。
> **後差しでOK＝P2／P3／P6**（枠のまま発売可）。揃わなくてもOP自体は枠で成立する。

## 出力仕様（厳守）

| 項目 | 値 |
|---|---|
| 解像度 | **1920×1080（16:9ちょうど）** ※ED画と同一 |
| 表示 | 全画面 `KEEP_ASPECT_COVERED`＝**比率が違うと自動トリミング**される。必ず16:9で出す |
| 焼き込みテキスト | **無し**（キャプションはエンジンがオーバーレイ＝i18nクリーン）。**例外＝P6のみ**（実ロゴは後合成・下記参照） |
| セーフゾーン | **下26%にキャプション暗帯**が乗る → **主役/焦点は上70%**に。下端は文字で隠れる前提 |
| 形式 | PNG。出力ファイル名＝`op_p1.png` … `op_p6.png`（厳守・コード側で参照済み） |
| 置き場所 | `SYNTAX_WARS/assets/art/opening/`（作成済み） |

## 共通スタイル（最重要）

- **絵柄の正＝ED画 `endings/nexus-7/nexus7_ed_p1.png` とアリーナbg `backgrounds/bg_arena_full.png`＝シネマティック実写寄り3Dレンダー風**（暗い・ボリューメトリック照明・濡れた反射）。**コミック/イラスト調ではない**。
- 同じED系でも2系統のムードがある：**ED画＝モノクロ青・極暗・静かな不安**／**アリーナbg＝マゼンタ+シアン高彩度・興行感**。パネルごとに寄せ先を指定（下記）。
- ステージ二色照明の基調＝**左マゼンタ／右シアン**（[[project-nexus7-color-stage]]）。

## AI生成ツール運用メモ（ChatGPT / Gemini）

OPは外注でなく**ChatGPT/Geminiで生成**する前提。プロンプトは英語＋[STYLE]＝そのまま使える。

- **★参照画像を添付する**（テキストだけより圧倒的に絵柄が揃う）：
  - 画風ref（毎回可）＝`endings/nexus-7/nexus7_ed_p1.png` ＋ `backgrounds/bg_arena_full.png`。
  - likeness用＝`characters/<char>/` の **`*_quote_pose.png`（顔の決め絵）/ `*_victory_pose.png`（全身）** か各キャラ**ED画**。
  - **⚠️ `*idle*` 系は全部スプライトシート＝添付禁止**（`*_idle.png`/`*_idle_FINAL.png`/`*_combat_idle.png`/`*.5x5`/`*.bak`。ピクセル絵のグリッドで画風汚染）。**目安＝正方形に近い寸法はシート**。
- **Gemini（Nano Banana）**＝キャラ一貫性が強い → **P4→P5でNEXUS/BLAZEを同じ顔に保つ**のに使う。**ChatGPT**＝対話で構図を詰める用。
- **P6だけ文字を生成させない**（両ツールとも文字崩れ）→ 背景生成→ロゴ後合成。
- 即使えるコピペシート＝`~/Desktop/SYNTAX_WARS素材/OP作画_2026-06-21/SYNTAX_OP作画プロンプト.md`（優先度P1/P4/P5先・各パネル＋添付ref付き）。

---

## パネル別

### P1〔ワイド・やや俯瞰〕アリーナ全景（ムード＝アリーナbg寄り）
満員の巨大アリーナ。観客席の向こう、無数の中継スクリーンに同じ一戦。世界中が見ている。
- 色：左マゼンタ／右シアンの二色照明＋客席の無数の光点。
- 重なるキャプション（参考）：「——世界が、見ている。」
- 参照：`bg_arena_full.png` は**色・照明の参照のみ**（中心アイレベル構図なので「俯瞰に加工」は不可）。構図は新規でやや俯瞰。

### P2〔インサート／放送テロップ〕中継グラフィック（ムード＝クール）
中継画面のテロップ枠。賞品＝"封じられた叡智への鍵"という**見世物のフレーム**（※裏の意味は出さない）。
- 色：放送グラフィックのクール系／白テロップ基調。
- 重なるキャプション（参考）：「勝者はひとり。手にするのは——封じられた叡智の、鍵。」
- 注：**意味を担う一文はオーバーレイ**＝画に焼かない。鍵アイコン＋放送UIの装飾グリフ（非言語）はOK。

### P3〔群像／ラインナップ〕6人の挑戦者（ムード＝多色・各固有ネオン）
6人が一画面。表情は読めない。それぞれの思惑を胸に。
- 色：各キャラ固有ネオン色が並ぶ＝多色。
- 重なるキャプション（参考）：「集うのは、6人。それぞれの思惑を、胸に抱えて。」
- **★並び順＝左から LYRA / BLAZE / SHADOW / NEXUS / NOVA / TITAN。TITANは他の約2倍体格（右端）**。
- **★既存idleはピクセルアートのスプライトシート＝参照添付は逆効果（絵柄汚染＋likeness取れず）。下のテキストでシルエット指定する**。

### P4〔対面・伏せ札〕二者至近で対峙（ムード＝ED寄り・脱彩度）★最重要①
二者が至近で向き合い、**互いの手はまだ伏せられている**＝開く直前の溜め。
- 色：緊張の青白い"間"／ED寄りの抑えた暗さ・二人の輪郭だけ固有色。
- 重なるキャプション（参考）：「ここでは、全部が同じ瞬間に開く。」
- **対＝左BLAZE（暖）・右NEXUS（冷）**（タイトル画面と同じ対）。

### P5〔同時開示・激突〕伏せ札が同時に開く瞬間（ムード＝開示の一閃）★最重要②
P4の続き。伏せられていた手が**同時に開き**、両者の一手が一斉に晒されてぶつかる＝ゲームの肝。
- 色：開示の一閃／P4と同じ対のまま、**シアン×暖色が交差**して光る（ここは彩度を上げてよい＝payoffの瞬間）。
- 重なるキャプション（参考）：「読み切った方が、勝つ。」
- **P4と同一の対・同一構図の"次の瞬間"**＝連続した2枚に見えること（伏せ→開示の対）。

### P6〔タイトルロゴ〕背景のみ生成→実ロゴ後合成
引き切り→暗転からロゴが灯る。
- **★生成AIに文字は描かせない（崩れる）。背景だけ生成→`SYNTAX WARS`ロゴ＋下に「READY?」をグラフィックツールで後合成**。
- キャプション＝無（text=""）。中央はロゴ配置用に暗く空ける。

---

## 依頼用・生成プロンプト（コピペ）

> 各プロンプト末尾に **[STYLE]** を貼る＝絵柄を揃える（**例外＝P2/P6は専用ブロック**）。

**[STYLE]（P1/P3/P4/P5共通・末尾に貼る）**
```
cinematic sci-fi concept art, photorealistic 3D render look, dark moody atmosphere,
volumetric neon lighting, wet reflective surfaces, dramatic rim light, high detail,
16:9 cinematic framing, keep the lower third darker and uncluttered for subtitle space,
no text, no words, no captions, no watermark, no logo, no UI frame
```

**P1 — アリーナ全景**
```
Ultra-wide establishing shot, slightly high angle bird's-eye, looking down over a colossal
packed futuristic combat arena. Tiered stands full of crowd fading into darkness; countless
giant broadcast screens ringing the arena all showing the same single duel. Split lighting:
magenta neon on the left, cyan neon on the right, wet reflective floor, thousands of tiny
crowd light points, high saturation, lively spectacle. [STYLE]
```

**P2 — 放送テロップ**（※[STYLE]は使わず下の専用版）
```
A sleek futuristic e-sports broadcast graphic floating over a dim arena: cool-toned
holographic HUD panels and a single glowing emblem of an ornate sealed key presented as the
grand prize, lens flares, shallow depth of field, clean cyan and white broadcast aesthetic.
cinematic photorealistic 3D render look, dark moody atmosphere, high detail, 16:9 framing,
sci-fi holographic glyphs and decorative shapes permitted, no readable real-language words,
no watermark.
```

**P3 — 6人ラインナップ**（参照添付しない・テキストでシルエット指定）
```
Dramatic group key-art lineup of six distinct cyberpunk fighters in a row, dark atmospheric
backdrop, each rim-lit in their own signature neon color, facing forward, unreadable
expressions. Left to right:
1. LYRA — hunched organic creature, bioluminescent green filaments across a dark body, tendril-like limbs
2. BLAZE — compact male fighter, red mohawk, dark armor with crimson-red accents, energy weapon at hip
3. SHADOW — cloaked figure in a black hood, glowing red eyes, long thin blade, cape billowing
4. NEXUS — lithe female android, dark tactical suit, dark blue-black hair, faint cyan glow at eyes and joints
5. NOVA — slender female, silver-white hair, long black coat with violet-purple lining
6. TITAN — massive armored mech suit, dark metallic plates, gold-amber accent lights, twice the height of the others
symmetrical cinematic composition. [STYLE]
```
> ※キャラ描写は art-director のスプライト読み取り。canon（character_arcs / 立ち絵）と突き合わせて微修正可。

**P4 — 対面・伏せ札（★最重要・脱彩度ED寄り）**
```
Two cyberpunk duelists facing each other in tight profile at very close range, a tense
standoff. Left fighter (BLAZE): compact, warm crimson-red rim light. Right fighter (NEXUS):
lithe female android, cool cyan rim light. Both have their arms deliberately lowered and held
close, their next move concealed — the charged stillness just before a simultaneous reveal.
deeply desaturated, near-monochrome cool tone, quiet ominous atmosphere, cold blue-white void. [STYLE]
```

**P5 — 同時開示・激突（★最重要・開示の一閃）**
```
Same two duelists as before, same close face-off — but THIS is the instant their concealed
moves are revealed at the same moment: both strike/open simultaneously, a symmetrical clash at
the exact center. Left BLAZE bursts warm crimson-orange, right NEXUS bursts cyan, the two
energies crossing in a bright flash at the meeting point, electric, decisive. higher saturation
burst of cyan and warm light at the clash, dramatic symmetrical composition. [STYLE]
```

**P6 — タイトルロゴ用・背景のみ**（※専用ブロック・文字は生成しない）
```
cinematic sci-fi concept art, photorealistic 3D render look, dark moody atmosphere near-black,
volumetric particle haze, subtle glowing circuit patterns in the background, cyan and magenta
accent particles, dramatic backlighting, high detail, 16:9 framing,
centered composition with empty dark space in the middle reserved for a title logo overlay,
no text, no words, no logo, no watermark
```
> 生成後＝中央の暗い余白に `SYNTAX WARS` ロゴ＋下に「READY?」をグラフィックツールで乗せる。

---

## 投入手順（ED方式）

1. 上記仕様で `op_p1.png`〜`op_p6.png`（1920×1080）を作る（P6は背景生成＋ロゴ後合成）
2. `SYNTAX_WARS/assets/art/opening/` に置く
3. Godotエディタで開く（自動import）or ヘッドレスimport
4. 枠（〔構図〕placeholder）が実画に切替＝完成。1枚ずつ後差しで可

## パネル別リスク（art-director）

| 難度 | パネル | 主リスク | 回避策（反映済） |
|---|---|---|---|
| 最高 | P3 | 6体likeness・サイズ比 | テキストシルエット＋TITAN2倍＋並び順明記 |
| 高 | P4/P5 | 「伏せ→開示」の概念が絵に出ない | 2枚連続＋"concealed→simultaneous reveal"明記 |
| 中 | P1 | 俯瞰変換の混乱 | bg参照は色照明のみ・構図は新規 |
| 低 | P2 | no-text矛盾 | P2専用スタイル（glyph許可） |
| 最低 | P6 | AIに文字を生成させる | 背景のみ生成→ロゴ後合成 |

## 着地

P1 見世物感 → P4→P5 メカ提示（同時開示＝伏せ→開示）→ P6 ロゴ「READY?」で**トレーラー頭に直結**。
「最強決定戦」ではなく「**読み合いの見世物**」としてフックする。
