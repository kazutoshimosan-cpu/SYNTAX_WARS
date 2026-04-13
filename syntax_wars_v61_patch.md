# SYNTAX WARS v6.0 → v6.1 バランス調整パッチ

## 概要
AUTO BATTLEシステム（100戦×30マッチアップ）によるデータ分析に基づくバランス調整。

---

## 1. キャラクターパラメータ変更

### SHADOW 🥷
- `dmgMod`: 1.0 → **0.8**
- 理由：速度＋2回行動の強さに対し、ダメージで相殺。コンセプトは「手数で削る」

### BLAZE 🔥
- `dmgMod`: 1.0 → **1.3**
- 理由：自爆リスクでバランスを取る設計。TITANと同火力だが命中ペナルティなし＆自爆あり

---

## 2. TITAN パッシブ「不動の要塞」強化

### 変更内容
SLOW状態（speed <= 35）の際：
- 全キャラ共通の「与ダメージ×0.8（SLOW penalty）」を**TITAN のみ無効化**
- 代わりに**与ダメージ×1.2（+20%バフ）**を付与
- 被ダメージ×0.75（-25%）はそのまま維持

### 実装箇所
`autoSlowMod(attacker, dmg)` 関数：
```javascript
function autoSlowMod(attacker, dmg) {
  if (!attacker.slow) return dmg;
  if (attacker.passive === 'titan') return dmg * 1.2; // SLOW時+20%
  return dmg * 0.8; // 通常SLOWペナルティ
}
```

通常バトル側の `applySlowMod` にも同様の処理を追加。

### コンセプト
「遅いほど一撃が重くなる重戦車」。相手に「スローを撃つと火力を上げてしまう」ジレンマを与える。

---

## 3. BLAZE 属性攻撃の修正

### 変更内容
属性攻撃3種（speedatk / guardbk / sealatk）に以下を適用：
- `× 1.4` の固有倍率を追加（`dmgMod`とは別枠）
- 自爆ダメージ：与ダメ×0.5 → **×0.2** に軽減
- 爆裂拳の自爆も同様に **×0.2** に統一

### 爆裂拳ダメージ変更
- `35 × dmgMod` → **固定45**（起爆準備後は45×1.3=**58**）
- `uniqueDesc` も更新

### 実装箇所（AUTO側・通常バトル側 両方）
```javascript
// speedatk / guardbk / sealatk のdmg確定後に追加
if (attacker.passive === 'blaze') dmg = Math.round(dmg * 1.4);

// 自爆
if (attacker.passive === 'blaze') { attacker.hp -= Math.round(dmg * 0.2); }
```

---

## 4. NEXUS-7 パッシブ「アドバンスド・ラーニング」修正・強化

### バグ修正
学習フラグ（`learned`）をセットする処理が**完全に欠落**していた。

### 修正内容
**① 学習セット**：相手が属性攻撃をヒットさせた時、NEXUS-7側に `learned` をセット
```javascript
// speedatk / guardbk / sealatk のヒット処理後に追加
if (defender.passive === 'learning') defender.learned = 'speedatk'; // 各攻撃種
```

**② 保持期間の変更**：「次のターンだけ」→「同じ属性を自分が使うまで保持」
```javascript
// 発動時のみリセット（ターン経過ではリセットしない）
if (attacker.learned === 'speedatk') { dmg = Math.round(dmg * 1.7); attacker.learned = null; }
```

**③ 属性攻撃バフ追加**：NEXUSの属性攻撃に `×1.1` を追加（学習発動とは別枠）
```javascript
if (attacker.passive === 'learning') dmg = Math.round(dmg * 1.1);
```

### 実装箇所
AUTO側・通常バトル側の speedatk / guardbk / sealatk 各3箇所（計6箇所）

---

## 5. BLAZE / NEXUS-7 属性攻撃バグ修正

### バグ内容
v7時点で属性攻撃（speedatk/guardbk/sealatk）に `× 1.5` のBLAZE専用バフが誤って実装されていた。
`dmgMod(1.2) × 1.5 = ×1.8` となりGDD仕様（全攻撃+20%）と乖離。

### 修正
`× 1.5` を削除。BLAZEの通常攻撃バフは `dmgMod:1.3` のみで表現。

---

## 6. HARD AI：BLAZE「起爆準備」メタ行動追加

### 追加仕様
`smartAiAction` 内に以下を追加：
```javascript
// 相手が起爆準備状態 → ゲージ50%以上ならカウンター優先（ウェイト+3）
if (opp.primed) {
  if (s.gauge >= 50 && s.gauge >= counterCost) { add('counter', 3); }
  else { add('guard', 3); if (s.gauge >= 30) add('slow', 2); }
}
```

### 調整経緯
初期実装はウェイト12で過剰。BLAZE勝率63%→33%に逆振れしたため、ウェイト3＋ゲージ50%条件に調整。

---

## 最終バランス結果（SMART AI、100戦×30マッチアップ）

| キャラ | 目標 | 結果 |
|---|---|---|
| NEXUS-7 | 45〜55% | ~53% ✅ |
| SHADOW | 45〜55% | ~53% ✅（要継続観察） |
| NOVA QUEEN | 45〜55% | ~49% ✅ |
| TITAN | 45〜55% | ~52% ✅ |
| LYRA-X | 45〜55% | ~53% ✅ |
| BLAZE | 45〜55% | ~47% ✅ |

**天敵関係（許容範囲）**
- LYRA-X → TITAN: ~66%
- TITAN → SHADOW: ~33%
