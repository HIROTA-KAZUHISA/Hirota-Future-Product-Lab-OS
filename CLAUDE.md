# CLAUDE.md

## プロジェクト概要

廣田（HIROTA-KAZUHISA）の展示会・視察・講演聴講の記録リポジトリ（プライベート）。
出張報告を単なる報告書で終わらせず、Claude Code のスキル（`.claude/commands/`）を使って
「レポート作成 → 編集 → 公開品質チェック → 知識資産化」まで一貫して行う。

仕組みは [Bishamon-Future-Product-Lab-OS](https://github.com/Yamazaki-27/Bishamon-Future-Product-Lab-OS)（山崎部長のリポジトリ）を踏襲している。

## ファイル構成

| ファイル/フォルダ | 内容 |
|---|---|
| `README.md` | リポジトリの説明・目次（レポート作成時に自動更新される） |
| `Reports/` | 展示会・視察レポート（`YYYYMM-イベント名/Report.md` 形式） |
| `Reports/archive_log.md` | 知識資産化の作業履歴（archive-report が自動生成・追記） |
| `KnowledgeBase/Knowledge/` | 技術テーマ別の知識ベースファイル |
| `KnowledgeBase/Companies/` | 訪問・接触企業の情報ファイル |
| `KnowledgeBase/Trends/` | 年別トレンドまとめ |
| `KnowledgeBase/Ideas/` | 商品開発・提案アイデアファイル |
| `.claude/commands/` | Claude Code 用スキルセット（下記ワークフロー参照） |

## レポート作成ワークフロー

新しい出張レポートを作る手順：

1. `Reports/YYYYMM-イベント名/` フォルダを作る（例：`Reports/202607-TechExpo/`）
2. その中に `Nippou.txt`（日報メモ）と `Images/`（写真）を入れる
3. Claude Code で `/build-report YYYYMM-イベント名` を実行

`/build-report` は以下を順番に自動実行する：

```
make-report    （著者）    Nippou.txt と写真から Report.md 初稿を作成
↓
review-report  （編集者）  写真配置・向き補正・本文の流れを改善
↓
publish-report （出版社）  GitHub 公開前の品質保証・CHANGELOG・公開判定
↓
archive-report （アーキビスト） 技術テーマ・企業・トレンド・アイデアを KnowledgeBase へ抽出
```

各工程を単独で実行することもできる（`.claude/commands/README.md` 参照）。
講演の文字起こしからレポートを作る場合は `make-lecture` を使う。

## 成果物（.mdファイル）の配置ルール

- スキルが生成する成果物の .md ファイルは、実行しているフォルダー直下に置く
- 写真・文字起こし等の元データを格納するサブフォルダー（`Images/`・`Knowledge/` 等）の中には置かない

## 画像ファイルの注意事項

- 写真はリサイズして容量を小さくしてから追加する（横幅800px程度）
- 動画は外部リンク（YouTube・Googleドライブ等）で共有する
- 1ファイル100MB超はGitHubにpush不可

## 実行環境について

- スキル内のコマンド例は macOS（`sips`・`ffmpeg`・BSD版 `stat`/`date`）を前提に書かれている
- Windows で実行する場合は ImageMagick（`magick`）・ffmpeg・PowerShell 等で同等の処理を行う
- リサイズ・回転時にファイルのタイムスタンプを保持する原則は環境によらず守る

## 文体サンプル

以下に廣田本人が書いた文章を数段落貼ると、生成レポートの文体が本人に近づく。

---

（今後追加：本人が書いたメール・報告書・メモなどから、素の文体がわかる文章を貼る）

---

### 文体の指示（サンプル追加までの暫定ルール）

- 結論を先に、理由は短く
- 事実と推測を混同しない。推測は推測と明記する
- 「〜と考えられます」「〜と思われます」は使わない
- 現場感・体験の生々しさを残す
- AIが書いたような均質で無機質な表現を避ける
