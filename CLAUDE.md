# 携帯キャリア家族4人プラン比較ページ

## 概要

`carrier-comparison.html` — 単一HTMLファイル（CSS・JS込み）。  
GitHub Pages で公開: https://kazsuya.github.io/mobile/carrier-comparison.html

---

## 家族構成・前提条件

| メンバー | 用途 | docomo プラン | au プラン |
|---------|------|--------------|----------|
| 大人① | 大容量 + 24h通話定額 | ahamo 大盛り 110GB + かけ放題 | au バリューリンク マネ活2 + 通話定額ライト2 |
| 大人② | 小容量 + 24h通話定額 | ドコモmini 4GB + かけ放題 | ahamo 30GB + かけ放題（auグループ外） |
| 子供① 9歳 | Apple Watch セルラー必須 | ドコモmini + ワンナンバー(550円) | U18バリュープラン + ナンバーシェア(385円) |
| 子供② 12歳 | 最安プラン | ドコモmini 4GB | U18バリュープラン |

**前提:** 全員iPhone利用 / docomo光契約あり / UQモバイルはApple Watchセルラー非対応のため対象外

---

## 比較パターン（4種）

| ID | キャリア | カード | 特徴 |
|----|---------|--------|------|
| p1 | docomo | dカード一般（年会費無料） | 現状パターン |
| p2 | docomo | dカードGOLD + ポイ活オプション | 年会費11,000円 |
| p3 | docomo | dカードPLATINUM + ポイ活オプション | 年会費29,700円 |
| au | au | au PAYゴールドカード | 年会費11,000円（大人①のみ） |

---

## コード内の修正頻度が高い定数

### 通信費（`DOCOMO_BASE` / auカード計算）

```javascript
const DOCOMO_BASE = {
  adult1: 6050,         // ahamo大盛り+かけ放題（固定・光割なし）
  adult2_base: 3520,    // ドコモmini+かけ放題-光割
  child1: 1100+550,     // ドコモmini+ワンナンバー
  child2: 1100,         // ドコモmini
};
```

### エンタメサブスク（`ENT_SERVICES` / `ENT_TOTAL`）

```javascript
const ENT_TOTAL = 5330;  // 2,290+1,280+1,760
const ENT_SERVICES = [
  { label: 'Netflix プレミアム', price: 2290 },
  { label: 'YouTube プレミアム', price: 1280 },
  { label: 'Google AI Pro',     price: 1760 },  // ★auキャンペーン価格
];
```

### ポイント還元定数

```javascript
// 爆アゲセレクション（ahamo回線のみ: P1/P2/P3共通）
// Netflix: 2,290÷1.1×10%→切上=209P  YouTube: 1,280÷1.1×5%→59P  Google AI Pro: 対象外
const BAKUAGE = { netflix: 209, youtube: 59, googleai: 0, total: 268 };

// au サブスクぷらすポイント（auマネ活プラン各種 20%）
// Netflix: 2,082×20%→417P  YouTube: 1,164×20%→233P  Google AI Pro: 1,600×20%→320P
const SABUUKU = { netflix: 417, youtube: 233, googleai: 320, total: 970 };
```

**ポイント計算ルール:** 税抜価格（÷1.1）×還元率、小数切上（`Math.ceil`）

---

## よくある修正パターン

### キャリア料金改定時
1. `DOCOMO_BASE` の該当プランの金額を更新
2. auパターンは `calcNet()` 内の `standard` 計算を更新
3. `最終更新` テキスト（HTML先頭付近の `.updated` クラス）を更新

### エンタメサブスク価格変更時
1. `ENT_SERVICES` の `price` を更新
2. `ENT_TOTAL` を合計値に更新
3. `BAKUAGE` / `SABUUKU` のポイント数を再計算して更新
   - 計算式: `Math.ceil(税込価格 / 1.1 × 還元率)`
4. 注釈セクション（HTML末尾 `<div class="notes">` 内）の説明文も更新

### 爆アゲ対象サービス変更時
- `BAKUAGE` 定数と対応するカード表示ロジック（`entRows` 変数）を更新
- 対象外サービスは `googleai: 0` のように0にする

---

## デプロイ手順

修正後は GitHub Pages に直接プッシュするだけで反映される。

```bash
# リポジトリをクローンして編集する場合
git clone https://github.com/KazSuya/mobile.git
cd mobile
# ... 編集 ...
git add carrier-comparison.html
git commit -m "料金更新: [変更内容]"
git push origin main
```

GitHub Actions（`pages-build-deployment`）が自動でビルド・デプロイ（約30〜60秒）。

---

## 注意事項

- **爆アゲセレクション**: 対象は ahamo 回線（大人①）のみ。ドコモmini回線は対象外。
- **Google AI Pro (au)**: auキャンペーン価格 1,760円（終了時期未定）。通常価格は Google One 2TB と同額の 1,450円。
- **au お買い物ポイント**: 上限 2,500P/月（月10万円利用で到達）。じぶん銀行残高50万円以上が条件。
- **ポイントは1P=1円**として実質額を計算（dポイント・Pontaポイントともに）。
