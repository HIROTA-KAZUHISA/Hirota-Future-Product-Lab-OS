# CLAUDE.md

## プロジェクト概要

廣田（HIROTA-KAZUHISA）の業務記録を知識資産に変えるリポジトリ（プライベート）。
Claude Code のスキル（`.claude/commands/`）で、3種類の情報源を扱う：

1. **視察・展示会**（Nippou.txt＋写真）→ Report.md → KnowledgeBase へ知識抽出
2. **会議音声**（録音 or 文字起こし）→ {フォルダ名}議事録.md → アクションアイテム集約
3. **販売実績**（CSV/Excel）→ SalesReport.md（グラフ付き分析）→ トレンド抽出

レポート系の仕組みは [Bishamon-Future-Product-Lab-OS](https://github.com/Yamazaki-27/Bishamon-Future-Product-Lab-OS)（山崎部長のリポジトリ）を踏襲し、議事録・販売分析は独自拡張。

## ファイル構成

| ファイル/フォルダ | 内容 |
|---|---|
| `README.md` | リポジトリの説明・目次（スキル実行時に自動更新される） |
| `Reports/` | 展示会・視察レポート（`YYYYMM-イベント名/Report.md` 形式） |
| `Reports/archive_log.md` | 知識資産化の作業履歴（archive-report が自動生成・追記） |
| `Meetings/` | 議事録（`YYYYMMDD-会議名/YYYYMMDD-会議名議事録.md` 形式） |
| `Meetings/ActionItems.md` | 全会議横断のアクションアイテム一覧（自動集約） |
| `Sales/Data/` | 販売実績の元データ（CSV/Excel エクスポート置き場） |
| `Sales/Reports/` | 販売分析レポート（グラフ付き） |
| `Sales/SCHEMA.md` | データ定義書（初回分析時に自動生成、要確認箇所を手動修正） |
| `KnowledgeBase/Knowledge/` | 技術テーマ別の知識ベースファイル |
| `KnowledgeBase/Companies/` | 訪問・接触企業の情報ファイル |
| `KnowledgeBase/Trends/` | 年別トレンドまとめ（販売実績からの示唆も集約） |
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

## 議事録ワークフロー

1. `Meetings/YYYYMMDD-会議名/` に音声ファイル（または transcript.txt）と Memo.txt を入れる
2. `/make-memo YYYYMMDD-会議名` を実行
3. {フォルダ名}議事録.md 生成 → ActionItems.md へアクション集約 → README 目次更新

**音声の文字起こしは必ずローカル（faster-whisper）で行う。クラウドAPIに送信しない。**
音声ファイルは .gitignore で除外されており、コミットされない。

## 販売実績分析ワークフロー

1. `Sales/Data/` に CSV / Excel エクスポートを置く
2. `/analyze-sales 対象期間` を実行（例：`/analyze-sales 2026-06`）
3. 初回は `Sales/SCHEMA.md`（データ定義書）が生成されるので、［要確認］箇所を修正する
4. `Sales/Reports/SalesReport_期間.md` がグラフ付きで生成される

**データに無い数字を作らない。データから言えないことは「判断できない」と書く。**

## 機密データの扱い

- このリポジトリはプライベート。**絶対に public に変更しない**
- 販売実績・議事録は機密。分析・文字起こしでデータを外部サービスに送信しない
- 音声ファイル（*.m4a 等）は .gitignore で除外。git に入れない

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
