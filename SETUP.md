# セットアップ手順

このリポジトリを自分のPCで使い始めるための手順。

## 1. 前提ツール

| ツール | 用途 | 入手 |
|---|---|---|
| Git | リポジトリの clone / push | https://git-scm.com/ |
| Claude Code | スキルの実行環境 | https://claude.com/claude-code |
| ImageMagick | 写真のリサイズ・回転（Windowsの場合） | `winget install ImageMagick.ImageMagick` |
| ffmpeg | 動画からのサムネイル抽出 | `winget install Gyan.FFmpeg`（macOSは `brew install ffmpeg`） |
| Python 3.10+ | 議事録の文字起こし・販売分析 | https://www.python.org/ |
| faster-whisper | 音声のローカル文字起こし（/make-memo 用） | `pip install faster-whisper` |
| pandas ほか | 販売実績の分析（/analyze-sales 用） | `pip install pandas matplotlib openpyxl` |

macOS の場合、画像処理は標準の `sips` が使われるので ImageMagick は不要。
Python 系は該当スキルを初めて使うときに入れればよい（スキルが案内する）。

## 2. リポジトリの取得

```bash
git clone https://github.com/HIROTA-KAZUHISA/Hirota-Future-Product-Lab-OS.git
cd Hirota-Future-Product-Lab-OS
```

プライベートリポジトリなので、HIROTA-KAZUHISA アカウントでの GitHub 認証が必要。

## 3. 最初にやること（推奨）

- `CLAUDE.md` の「文体サンプル」欄に、自分が書いたメール・報告書などの文章を数段落貼る
  → 生成されるレポートの文体が本人に近づく
- `.claude/commands/Config/report-config.yml` の `company`・`department` を実際の値に更新する

## 4. レポートを作る

[Reports/_TEMPLATE/README.md](Reports/_TEMPLATE/README.md) の手順に従う。要点：

```text
1. Reports/_TEMPLATE をコピーして Reports/YYYYMM-イベント名/ を作る
2. Nippou.txt（日報メモ）を書き、Images/ に写真を入れる
3. Claude Code で /build-report YYYYMM-イベント名 を実行
```

## 5. GitHubへの反映

スキルは git commit / push を自動実行しない（推奨コミットメッセージだけ提示される）。
内容を確認してから手動で push する：

```bash
git status
git add .
git commit -m "docs(フォルダ名): 変更内容の要約"
git push
```

## 6. 戦略アドバイザースキルを追加したい場合

書籍・資料の知識ベースに基づく専門アドバイザー（山崎さんの /dx-strategy・/infra-mente 相当）を作るには、
[.claude/commands/Examples/domain-advisor-template.md](.claude/commands/Examples/domain-advisor-template.md) を参照。

## トラブルシューティング

- **スキルが認識されない**：リポジトリのルートフォルダで Claude Code を起動しているか確認する（`.claude/` が見えるフォルダ）
- **写真の向きがおかしい**：`review-report` を再実行すると EXIF に基づいて自動補正される
- **100MB超のファイルが push できない**：GitHub の制限。動画は外部リンク（YouTube・Googleドライブ等）で共有する
