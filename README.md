# Hirota Future Product Lab OS

展示会・視察・講演のレポートを作成し、会社の知識資産として蓄積するリポジトリ。
Claude Code 用スキルセット（`.claude/commands/`）を同梱している。

仕組みは [Bishamon-Future-Product-Lab-OS](https://github.com/Yamazaki-27/Bishamon-Future-Product-Lab-OS) を踏襲。

---

## クイックスタート

```text
1. Reports/YYYYMM-イベント名/ フォルダを作る
2. その中に Nippou.txt（日報メモ）と Images/（写真）を入れる
3. Claude Code でこのリポジトリを開き、以下を実行：

   /build-report YYYYMM-イベント名
```

これだけで「初稿作成 → 編集 → 公開前チェック → 知識資産化」まで自動で実行される。

## スキル一覧

| スキル | 役割 | 説明 |
|---|---|---|
| `/build-report` | 統括役 | 下記4工程を順番に一括実行する |
| `make-report` | 著者 | Nippou.txt と写真から Report.md 初稿を作る |
| `review-report` | 編集者 | 写真配置・向き補正・本文の流れを整える |
| `publish-report` | 出版社 | GitHub 公開前の品質保証・公開判定を行う |
| `archive-report` | 知識アーキビスト | Report.md から技術・企業・トレンド・アイデアを抽出する |
| `make-lecture` | 講演記録係 | 講演の文字起こしと写真から Lecture.md を作る |

---

## 出張報告書

| 日付 | 出張先 | 参加者 | 写真枚数・容量 | ナレッジ化 |
|---|---|---|:---:|:---:|

<br>

## 講演会レポート

| 日付 | 講演・イベント | 講師 | 聴講者 |
|---|---|---|:---:|

<br>

---

## 知識ベース（Knowledge Base）

展示会・出張レポートから抽出した技術テーマ・企業情報・市場変化・商品開発アイデアの蓄積。
`archive-report` 実行時にこのセクションが自動更新される。

（まだレポートがありません。最初のレポートを作成すると、ここに目次が生成されます）
