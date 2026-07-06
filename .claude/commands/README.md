# Hirota Future Product Lab Report System v1.0

展示会・海外視察・技術調査レポートを、単なる報告書ではなく、会社の知識資産へ育てるための Claude Code 用スキルセットです。

## 構成

```text
Skills/
  review-report.md
  publish-report.md
  archive-report.md
  build-report.md

Config/
  FPL_STYLE.md
  report-config.yml
```

## 推奨ワークフロー

```text
make-report.md
↓
review-report.md
↓
publish-report.md
↓
archive-report.md
```

## 各スキルの役割

### make-report.md

初稿作成。Nippou.txtと写真をもとにReport.mdを作る。

### review-report.md

編集者。写真・本文・構成を整える。

### publish-report.md

出版社。GitHub公開前の品質保証を行う。

### archive-report.md

知識アーキビスト。Report.mdから技術テーマ・企業・トレンド・アイデアを抽出する。

### build-report.md

統括役。各工程を順番に実行する。

## 配置例

Claude Code のプロジェクトに以下のように配置します。

```text
.claude/
  skills/
    review-report/
      review-report.md
    publish-report/
      publish-report.md
    archive-report/
      archive-report.md
    build-report/
      build-report.md
```

`Config/` はリポジトリ直下、または `.claude/` 配下に置いてください。

## 使い方

### 公開前チェックだけ行う場合

```text
publish-report.md のスキルを使って、現在の Report.md をGitHub公開前の品質で最終確認してください。画像リンク、Markdown表示、README.md、CHANGELOG.md、Release Notes、公開判定まで実施してください。
```

### 編集から公開まで行う場合

```text
review-report.md で Report.md を編集品質まで高めたあと、publish-report.md で公開前チェックを行ってください。
```

### 知識資産化まで行う場合

```text
build-report.md の考え方に従って、Report.md を公開品質に整えたうえで、archive-report.md により Knowledge、Companies、Trends、Ideas を更新してください。
```

## Version

v1.0

