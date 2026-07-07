# Hirota Future Product Lab OS

**視察・会議・数字** の3つの情報源を、Claude Code のスキルで知識資産に変えるリポジトリ。

| 柱 | 入力 | 出力 | スキル |
|---|---|---|---|
| 🔍 視察・展示会 | 日報メモ＋写真 | Report.md＋知識ベース | `/build-report` |
| 🎙️ 会議 | 音声 or 文字起こし | {フォルダ名}議事録.md＋アクション一覧 | `/make-memo` |
| 📊 販売実績 | CSV / Excel | グラフ付き分析レポート | `/analyze-sales` |

仕組みは [Bishamon-Future-Product-Lab-OS](https://github.com/Yamazaki-27/Bishamon-Future-Product-Lab-OS) を踏襲。

📖 **[なぜ GitHub の上に作られているのか（解説書）](WHY-GITHUB.md)**

---

## クイックスタート

初回のみ [SETUP.md](SETUP.md) の手順で環境を準備する。以降は：

```text
1. Reports/_TEMPLATE をコピーして Reports/YYYYMM-イベント名/ を作る
2. Nippou.txt（日報メモ）を書き、Images/（写真）を入れる
3. Claude Code でこのリポジトリを開き、以下を実行：

   /build-report YYYYMM-イベント名
```

これだけで「初稿作成 → 編集 → 公開前チェック → 知識資産化」まで自動で実行される。
詳しい準備手順は [Reports/_TEMPLATE/README.md](Reports/_TEMPLATE/README.md) を参照。

## スキル一覧

| スキル | 役割 | 説明 |
|---|---|---|
| `/build-report` | 統括役 | 下記4工程を順番に一括実行する |
| `make-report` | 著者 | Nippou.txt と写真から Report.md 初稿を作る |
| `review-report` | 編集者 | 写真配置・向き補正・本文の流れを整える |
| `publish-report` | 出版社 | GitHub 公開前の品質保証・公開判定を行う |
| `archive-report` | 知識アーキビスト | Report.md から技術・企業・トレンド・アイデアを抽出する |
| `make-lecture` | 講演記録係 | 講演の文字起こしと写真から Lecture.md を作る |
| `make-memo` | 書記 | 会議音声をローカルで文字起こしし、{フォルダ名}議事録.md とアクション一覧を作る |
| `analyze-sales` | データアナリスト | 販売実績データからグラフ付き分析レポートを作る |

書籍・資料の知識ベースに基づく専門アドバイザースキル（山崎さんの /dx-strategy 相当）を追加する場合は
[domain-advisor-template.md](.claude/commands/Examples/domain-advisor-template.md) を使う。

---

## 出張報告書

| 日付 | 出張先 | 参加者 | 写真枚数・容量 | ナレッジ化 |
|---|---|---|:---:|:---:|

<br>

## 講演会レポート

| 日付 | 講演・イベント | 講師 | 聴講者 |
|---|---|---|:---:|

<br>

## 議事録

📌 **[アクションアイテム一覧（全会議横断）](Meetings/ActionItems.md)** ／ 準備手順：[Meetings/_TEMPLATE](Meetings/_TEMPLATE/README.md)

| 日付 | 会議名 | 出席者 | アクション数 |
|---|---|---|:---:|
| 2026年7月1日 | [テスト録音（動作検証）](Meetings/20260701-テスト録音/20260701-テスト録音議事録.md) | ［要確認］ | 0件 |
| 2026年7月3日 | [物流展](Meetings/20260703物流展/20260703物流展議事録.md) | ［要確認］ | 1件 |

<br>

## 販売実績レポート

使い方：[Sales/README.md](Sales/README.md)

| 対象期間 | レポート | データ最終日 | 作成日 |
|---|---|:---:|:---:|

<br>

---

## 知識ベース（Knowledge Base）

展示会・出張レポートから抽出した技術テーマ・企業情報・市場変化・商品開発アイデアの蓄積。
`archive-report` 実行時にこのセクションが自動更新される。

（まだレポートがありません。最初のレポートを作成すると、ここに目次が生成されます）
