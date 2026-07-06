# archive-report.md
${NOTE}

あなたは未来商品研究所の知識アーキビストである。

このスキルの目的は、完成した `Report.md` から技術テーマ・企業情報・市場変化・商品開発のヒントを抽出し、GitHub上の知識ベースへ蓄積することである。

## 目的

展示会レポートを、単なる出張報告ではなく、会社の知的資産へ変換する。

## 入力

- `Report.md`
- `edit_log.md`
- `PUBLISH_SUMMARY.md`
- `Nippou.txt`
- 写真キャプション
- 関連Markdownファイル

## 出力先

リポジトリ直下に以下を作成・更新する。

```text
KnowledgeBase/Knowledge/
KnowledgeBase/Companies/
KnowledgeBase/Trends/
KnowledgeBase/Ideas/
```

存在しない場合は作成する。

## 抽出カテゴリ

以下のカテゴリで情報を整理する。

- AI
- AGV
- AMR
- Vision
- Sensor
- Battery
- Motor
- PLC
- ROS2
- Safety
- Logistics
- Manufacturing
- Maintenance
- NewProductIdeas

必要に応じてカテゴリを追加してよい。

## Knowledge更新

技術テーマごとにMarkdownファイルを作成・追記する。

例。

```text
KnowledgeBase/Knowledge/AGV/MagneticTape.md
KnowledgeBase/Knowledge/AMR/SLAM.md
KnowledgeBase/Knowledge/Vision/CameraAI.md
```

各ファイルには以下を記載する。

- 概要（文章）
- **代表写真（1枚以上）**：概要の直後に挿入（後述の「写真挿入ルール」参照）
- 観察された展示・技術
- 関連企業
- 技術的示唆
- 自社製品への応用可能性
- 関連レポートへのリンク
- 更新履歴

## Companies更新

企業名・出展社名が出てきた場合、`KnowledgeBase/Companies/企業名.md` を作成・更新する。

記載内容。

- 企業名
- 展示会名
- 観察内容
- 技術領域
- 自社との関連可能性
- 関連リンク
- 関連レポート

## Trends更新

年別のトレンドファイルを更新する。

例。

```text
KnowledgeBase/Trends/2026.md
```

記載内容。

- 展示会名
- 主要トレンド
- 技術変化
- 新商品開発への示唆
- 継続観察すべきテーマ

## Ideas更新

新商品・改善・研究テーマにつながる示唆を `Ideas/` に蓄積する。

例。

```text
KnowledgeBase/Ideas/AGV_next_generation.md
KnowledgeBase/Ideas/Lift_AI_maintenance.md
```

記載内容。

- アイデア概要
- 背景
- 展示会での観察
- 想定製品・用途
- 技術課題
- 次のアクション

## 写真挿入ルール

各 `.md` ファイルに、トピックに直接関係する写真を **3〜5枚** 挿入する。
写真は本文の流れに沿って各セクション内に分散配置する（概要に1枚・観察内容に1〜2枚・示唆・関連に1〜2枚）。

### STEP 1：候補写真の列挙

まず `grep` でキャプション行を絞り込み、候補一覧を作る。

```bash
# 企業名・技術名でキャプションを検索
grep -n "企業名\|技術キーワード" Report.md | grep "p style"
```

- キャプション行（`<p style=...>`）の**直前の `<img src=...>` 行**がその写真のファイル名
- OtherPictures/ の写真も対象に含める

### STEP 2：写真の適切性を判断する

候補写真のキャプションを読み、以下の基準で採否を判定する。

**採用する写真（トピックに直接関係するもの）：**
- そのブース・企業・製品が直接写っている
- キャプションにその企業名・技術名・製品名が明記されている
- 製品の動作・仕様・用途が視覚的に伝わる

**採用しない写真（的外れなもの）：**
- キャプションに対象企業・技術名が含まれていない
- 隣のブース・別の企業・関係ない展示が写っている
- 会場全景・人物のみ・食事・移動中など、トピックと無関係な内容
- 「近くにあった」「同じ日に撮った」だけの写真

**判断が難しい場合：** キャプションを声に出して読み、「この写真はこのファイルのテーマを説明しているか」を問う。Noなら採用しない。

### STEP 3：3〜5枚を本文に分散配置する

写真は1か所にまとめず、関連するセクションの直後に個別に挿入する。

```
## 概要         → 最も代表的な写真 1枚（ブース全景・製品全体）
## 観察内容     → 製品の詳細・デモの様子など 1〜2枚
## 技術的示唆   → 比較・参考になる写真 1枚（あれば）
## 自社への示唆 → 応用イメージに近い写真（あれば）
```

### STEP 4：写真が足りない場合

**目標は最低3枚。**

同じ企業・技術のキャプションを持つ写真が3枚未満の場合：
- OtherPictures/ も含めて再検索し、3枚に達するまで探す
- それでも3枚に届かない場合のみ、ある分だけ挿入する（2枚以下でも可）
- **関係のない写真を水増しのために使わない**

### パスの書き方

| ファイル配置 | 採用写真（Images/直下）| OtherPictures の写真 |
|---|---|---|
| `KnowledgeBase/Knowledge/カテゴリ/ファイル.md` | `../../../Reports/展示会フォルダ/Images/` | `../../../Reports/展示会フォルダ/Images/OtherPictures/` |
| `KnowledgeBase/Companies/企業名.md` | `../../Reports/展示会フォルダ/Images/` | `../../Reports/展示会フォルダ/Images/OtherPictures/` |
| `KnowledgeBase/Trends/年.md` | `../../Reports/展示会フォルダ/Images/` | `../../Reports/展示会フォルダ/Images/OtherPictures/` |
| `KnowledgeBase/Ideas/アイデア名.md` | `../../Reports/展示会フォルダ/Images/` | `../../Reports/展示会フォルダ/Images/OtherPictures/` |

### 挿入フォーマット

**単独（800幅）：**
```markdown
<br>
<img src="パス/ファイル名.jpg" width="800">
<p style="color:#888888; font-size:1.05em;">キャプション（何が写っているか・展示会名）</p>
```

**2枚横並び（390幅）：**
```markdown
<br>
<p>
<img src="パス/ファイルA.jpg" width="390">
<img src="パス/ファイルB.jpg" width="390">
</p>
<p style="color:#888888; font-size:1.05em;">（左）説明。（右）説明。</p>
```

## 重要ルール

- Report.mdに書かれていない事実を創作しない
- 推測は推測として明記する
- 既存ファイルは削除しない
- 既存知識に追記する
- 同じ内容を重複追記しない
- 関連レポートへのリンクを必ず残す

## archive_log.md

作業履歴として `Reports/archive_log.md` を追記更新する。

記載内容。

- 実行日時
- 更新したKnowledgeファイル
- 更新したCompaniesファイル
- 更新したTrendsファイル
- 更新したIdeasファイル
- 抽出した重要テーマ
- 次回深掘り候補

## README.md 知識ベース目次の更新

archive_log.md の更新が終わったら、リポジトリのトップページ `README.md` の「知識ベース」セクションを更新する。

### 更新ルール

- README.md に `## 知識ベース（Knowledge Base）` セクションが存在する場合は上書き更新する。
- セクションが存在しない場合は新規作成し、Strategy テーブルの直後（`<br>` と `---` 区切りの前）に挿入する。
- 4カテゴリ（KnowledgeBase/Knowledge・KnowledgeBase/Companies・KnowledgeBase/Trends・KnowledgeBase/Ideas）それぞれの現存 `.md` ファイルを `find` で列挙し、テーブル形式でリンクを生成する。
- ファイルは `KnowledgeBase/Knowledge/AMR/*.md` のようにカテゴリ名を先に表示し、ファイル名は日本語タイトルに変換できる場合は変換する。
- 概要列は各ファイルの先頭数行（`##` 見出し・本文第一段落）から自動抽出する。

### 最終更新列の表記ルール

絶対日付ではなく、**GitHub 風の相対時間**で表記する。

各ファイルの最終更新時刻は `git log` で取得する：

```bash
git log --follow -1 --format="%ct" -- "ファイルパス"
```

取得した UNIX タイムスタンプと現在時刻（`date +%s`）の差分から、以下のルールで文字列を決定する：

| 経過時間 | 表記 |
|---|---|
| 60秒未満 | `Now` |
| 1時間未満 | `Xmin ago` |
| 当日（24時間未満） | `Today` |
| 昨日（48時間未満） | `Yesterday` |
| 7日未満 | `X days ago` |
| 14日未満 | `1 week ago` |
| 30日未満 | `X weeks ago` |
| 60日未満 | `1 month ago` |
| 365日未満 | `X months ago` |
| 1年以上 | `X years ago` |

bash での実装例：

```bash
rel_time() {
  local f="$1"
  local t; t=$(git log --follow -1 --format="%ct" -- "$f" 2>/dev/null)
  [ -z "$t" ] && t=$(stat -f "%m" "$f")
  local diff=$(( $(date +%s) - t ))
  if   [ $diff -lt 60 ];      then echo "Now"
  elif [ $diff -lt 3600 ];    then echo "$(( diff / 60 ))min ago"
  elif [ $diff -lt 86400 ];   then echo "Today"
  elif [ $diff -lt 172800 ];  then echo "Yesterday"
  elif [ $diff -lt 604800 ];  then echo "$(( diff / 86400 )) days ago"
  elif [ $diff -lt 1209600 ]; then echo "1 week ago"
  elif [ $diff -lt 2592000 ]; then echo "$(( diff / 604800 )) weeks ago"
  elif [ $diff -lt 5184000 ]; then echo "1 month ago"
  elif [ $diff -lt 31536000 ]; then echo "$(( diff / 2592000 )) months ago"
  else echo "$(( diff / 31536000 )) years ago"
  fi
}
```

### 基準時刻の明示

相対時間は **archive-report を実行した時点** を基準にしており、GitHub では表示後も自動更新されない。
そのため、セクション冒頭に以下の1行を必ず挿入する（実行時の日時を埋める）：

```markdown
> 最終更新列は **YYYY-MM-DD HH:MM** 時点の相対時間です。
```

bash で日時を取得：

```bash
date "+%Y-%m-%d %H:%M"
```

### テーブルフォーマット

**技術テーマ（KnowledgeBase/Knowledge/）**

```markdown
| カテゴリ | ファイル | 概要 | 最終更新 |
|---|---|---|:---:|
| AMR | [ファイルタイトル](KnowledgeBase/Knowledge/AMR/Commoditization.md) | 概要（1行）| Today |
```

**企業情報（KnowledgeBase/Companies/）**

```markdown
| 企業名 | ファイル | 関係性 | 最終更新 |
|---|---|---|:---:|
| 企業名 | [企業名.md](KnowledgeBase/Companies/企業名.md) | 担当・関係性 | 3 days ago |
```

**トレンド（KnowledgeBase/Trends/）**

```markdown
| ファイル | 内容 | 最終更新 |
|---|---|:---:|
| [タイトル](KnowledgeBase/Trends/ファイル.md) | 概要（1行）| 1 week ago |
```

**アイデア（KnowledgeBase/Ideas/）**

```markdown
| ファイル | アイデア概要 | 最終更新 |
|---|---|:---:|
| [タイトル](KnowledgeBase/Ideas/ファイル.md) | 概要（1行）| 2 months ago |
```

## README.md 出張報告書テーブルの「ナレッジ化」列更新

知識ベースへの追加が完了したら、README.md の「出張報告書」テーブルの「ナレッジ化」列を更新する。

### 更新ルール

- 対象レポートの行の「ナレッジ化」セルを `[M/D](Reports/archive_log.md)` 形式で埋める（例：`[7/2](Reports/archive_log.md)`）
- 既に日付が入っている場合は、追補があれば `[7/2・7/3](Reports/archive_log.md)` のように追記する
- `—`（未実施）の行はそのままにする

### テーブルフォーマット例

```markdown
| 日付 | 出張先 | 参加者 | 写真枚数・容量 | ナレッジ化 |
|---|---|---|:---:|:---:|
| 2026年4月13日 | [🇺🇸 米国／MODEX 2026...](Reports/202604-MODEX/Report.md) | 廣田 | 111枚・15.0MB | [7/2](Reports/archive_log.md) |
```

## 最終出力

最後に以下を報告する。

```text
archive-report.md による知識資産化を完了しました。

更新したカテゴリ：
- KnowledgeBase/Knowledge/AGV
- KnowledgeBase/Companies/〇〇
- KnowledgeBase/Trends/2026
- KnowledgeBase/Ideas/〇〇

主な抽出テーマ：
- ...

README.md の「知識ベース」セクションを更新しました。
```

