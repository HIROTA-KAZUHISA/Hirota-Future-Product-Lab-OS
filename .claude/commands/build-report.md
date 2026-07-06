# build-report.md

あなたは Hirota Future Product Lab Report System のビルド責任者である。

このスキルは、展示会レポート作成の一連の流れを統括する。

---

## 引数の受け取り方

`/build-report <フォルダー名>` の形式で呼び出される。

例：`/build-report 202604-HANNOVER`

- 引数として渡されたフォルダー名を `Reports/` 直下（見つからない場合はリポジトリ直下）のフォルダーとして特定する
- 以降の全工程において、このフォルダーを作業対象とする
- フォルダーが存在しない場合は、そのことを報告して停止する

---

## 実行方針

各工程を順番にすべて実行する。

以下の停止を発生させない。

- 「次に進めますか？」
- 「実行してよいですか？」
- 「確認してください」

ただし、**停止条件**（後述）に該当した場合は例外とする。

---

## 工程と実行ファイル

以下の順番で実行する。各工程は、対応するスキルファイルを必ず読み込んでから実行する。

```
1. make-report
   スキルファイル: .claude/commands/make-report.md

2. review-report
   スキルファイル: .claude/commands/review-report.md

3. publish-report
   スキルファイル: .claude/commands/publish-report.md

4. archive-report
   スキルファイル: .claude/commands/archive-report.md
```

各スキルファイルを Read ツールで読み込み、その指示に完全に従って実行すること。
「相当の処理」と解釈して独自実装しない。必ずスキルファイルを読む。

各工程の許可設定（確認不要・即実行の範囲）はそれぞれのスキルファイルの指示に従う。
動画ファイルの削除（rm）を含むすべての操作も、各スキルファイルに許可が明記されていれば確認不要で実行する。

---

## 完了済み判定

各工程の実行前に、以下の2段階で「前回ビルドの状態」を確認する。

### 第1段階：前回の中断工程の確認

`build_log.md` が存在する場合、最終エントリを読み、前回どの工程まで完了したかを特定する。

- 前回が **工程の途中で中断**（例：make-report・review-report 完了、publish-report 未実行）している場合は、
  ファイルのタイムスタンプ比較を行わず、**未完了の工程から再開する**
- この場合、make-report を再実行しない（Report.md が PUBLISH_SUMMARY.md より新しくても「追記モード」と誤判定しない）
- 前回のビルドが archive-report まで完走している場合のみ、第2段階へ進む

### 第2段階：ファイル変更の確認

確認対象は `Report.md` だけでなく、**写真・動画・日報テキスト** も含む。
日報ファイルはファイル名の揺れ（タイポ・別名）があり得るため、`*.txt` 全体を対象とする。

```bash
# 作業対象フォルダー内で実行
if [ ! -f PUBLISH_SUMMARY.md ]; then
  # PUBLISH_SUMMARY.md が無い＝初回ビルド。必ず「変更あり」として全工程を実行する
  echo "変更あり（初回ビルド）"
else
  # PUBLISH_SUMMARY.md より新しいファイルが1つでもあれば「変更あり」
  newer=$(find . \
    \( -name "Report.md" \
    -o -name "*.txt" \
    -o -path "*/Images/*" \) \
    -newer PUBLISH_SUMMARY.md 2>/dev/null | head -1)
  # $newer が空なら「変更なし」、空でなければ「変更あり」
fi
```

**注意**：`find` の `-newer` は比較対象ファイルが存在しないとエラーになり何も返さない。
`2>/dev/null` でエラーが隠れると初回ビルドを「変更なし」と誤判定するため、存在チェックを必ず先に行う。

**変更なし**と判定された場合（`PUBLISH_SUMMARY.md` が存在し、上記のファイルがすべて `PUBLISH_SUMMARY.md` 以前のタイムスタンプ）：

- **make-report は「写真・動画審査のみモード」で必ず実行する**（Report.md は変更しない）
- review-report・publish-report・archive-report をスキップする
- 以下を出力する：

```text
前回ビルドから変更なし。

対象フォルダー：<フォルダー名>
PUBLISH_SUMMARY.md 更新日時：<日時>

写真・動画審査のみ実行します。
Report.md・Nippou.txt の変更があれば、再実行時に追記します。
```

**変更あり**と判定された場合（`PUBLISH_SUMMARY.md` が存在しない、または上記ファイルのいずれかが `PUBLISH_SUMMARY.md` より新しい）：

- 変更があったファイルを特定してログに残す
- 通常通り全工程を実行する

---

## 工程の実行条件

### 1. make-report

**写真・動画の審査（サムネイル抽出・採否判断・OtherPictures/unUsed への振り分け）は、毎回必ず実行する。**

実行モードは以下の条件で決まる：

| 条件 | モード | Report.md の扱い |
|---|---|---|
| `Report.md` が存在しない | 初稿作成モード | 新規作成 |
| `Nippou.txt` が `PUBLISH_SUMMARY.md` より新しい | 追記モード | 差分を末尾に追記 |
| `Images/` に `PUBLISH_SUMMARY.md` より新しいファイルがある | 追記モード | 差分を末尾に追記 |
| 明示的に初稿作成・再生成を求められた | 初稿作成モード | 新規作成 |
| 上記いずれにも該当しない | **写真・動画審査のみ** | **変更しない** |

追記モードの場合、make-report.md の指示に従い、既存の `Report.md` の内容を変更せず、追加分のみ末尾に追記する。

目的。

- 動画サムネイル抽出・動画本体削除（毎回）
- 写真審査（毎回）：**基本は全写真を本文採用**。採用写真とよく似た写真のみ OtherPictures へ、ピンぼけ・手ブレ・誤写・ほぼ同一画角のみ unUsed へ
- Nippou.txt を中心に初稿作成または追記
- 写真を時系列で配置
- 展示会概要を補足
- README.md へ追加

### 2. review-report

`Report.md` が存在する場合に実行する（make-report を実行した直後を含む）。

完了済み判定で「変更なし」と判定された場合はスキップする。

目的。

- 写真と本文の整合性確認
- 写真の向き補正
- 文章・構成の改善
- edit_log.md 更新

### 3. publish-report

review-report が完了した後に実行する。

完了済み判定で「変更なし」と判定された場合はスキップする。

目的。

- Markdown 品質確認
- GitHub 表示確認
- 画像リンク確認
- README 確認
- CHANGELOG 更新
- Release Notes 生成
- 公開判定

### 4. archive-report

publish-report の判定が `Ready for Publish` の場合のみ実行する。

`Not Ready` または `Needs Review` の場合は archive-report をスキップし、その理由を `build_log.md` に記録する。

完了済み判定で「変更なし」と判定された場合はスキップする。

目的。

- 技術テーマ抽出
- Knowledge 更新
- Companies 更新
- Trends 更新
- Ideas 更新

---

## build_log.md

作業対象フォルダー直下（例：`202604-HANNOVER/build_log.md`）に作成・追記する。
毎回作り直さず、履歴として追記する。

テンプレート。

```markdown
# build_log — <フォルダー名>

## YYYY-MM-DD ビルド実行

### 実行工程

| 工程 | 結果 | 備考 |
|---|---|---|
| make-report | done / skipped / failed | 写真分類の内訳・実行モード等 |
| review-report | done / skipped / failed | 修正件数等 |
| publish-report | Ready for Publish（XX/100）/ Needs Review / Not Ready | スコア |
| archive-report | done / skipped / failed | 更新した KnowledgeBase ファイル数 |

### 生成・更新ファイル

- （工程ごとに列挙）

### 特記事項

- （エラー・代替手段・［要確認：〇〇］等）

### 次に必要な工程

- （全完了なら「なし（git commit のみ）」。中断時は未完了工程名を明記 ← 再開判定に使う）
```

**「次に必要な工程」欄は再開判定（完了済み判定・第1段階）が参照するため、必ず記入する。**

---

## 停止条件

以下の場合は、次工程へ進まず `build_log.md` に理由を記録して停止する。

- 引数のフォルダーが存在しない
- `Report.md` が存在せず、make-report に必要な情報（Nippou.txt・写真）もない
- 画像リンク切れが多数あり、自動修正不能
- publish 判定が `Not Ready`
- 事実確認が必要な未確認事項が多く、処理を続けると誤情報が残る

---

## エラー時の処理

停止条件に該当しないエラーが発生した場合。

1. 可能な範囲で代替手段を試す
2. 代替手段でも失敗した場合は、失敗内容を `build_log.md` に記録する
3. 次の工程へ進む

不明点がある場合も質問せず、`［要確認：〇〇が不明］` と記録して処理を継続する。

---

## コンテキスト窓について

make-report は大量のテキスト・写真処理を行うため、コンテキストを大量に消費する。

一括実行の途中でコンテキストが逼迫した場合は、その工程の完了をもって一旦区切りとし、
**`build_log.md` に「どの工程まで完了したか・次に必要な工程」を必ず記録してから終了する**。

次のセッションで `/build-report` を再実行すると、完了済み判定の第1段階が `build_log.md` の最終エントリを読み、未完了の工程から再開する。

---

## 時刻の計測

ビルド開始時と各工程の完了時に `date +%s` でUNIX時刻を記録する。

```bash
# 例：開始時
t_start=$(date +%s)

# 例：make-report 完了後
t_make=$(date +%s)

# 例：経過秒数の計算
elapsed=$(( t_make - t_start ))
# 分・秒表示に変換
printf "%d分%02d秒" $(( elapsed / 60 )) $(( elapsed % 60 ))
```

各工程の開始・終了のタイミングで `date +%s` を取得し、工程別の所要時間と合計時間を最終出力に含める。

---

## 最終出力

全工程が終わった後、以下を出力する。

```text
Future Product Lab Report System build 完了

対象フォルダー：<フォルダー名>
開始時刻：HH:MM:SS
終了時刻：HH:MM:SS

実行工程：
- make-report:    done（X分XX秒）/ skipped（理由）/ failed（理由）
- review-report:  done（X分XX秒）/ skipped（理由）/ failed（理由）
- publish-report: Ready for Publish（X分XX秒）/ Needs Review / Not Ready
- archive-report: done（X分XX秒）/ skipped（理由）/ failed（理由）

合計時間：X分XX秒

写真統計：
- 採用（本編）：XX枚
- OtherPictures：XX枚
- unUsed：XX枚
- 動画サムネイル：XX本（元動画は削除済み）

要確認事項：
- （あれば列挙）

変更ファイル：
- （主要なものを列挙）

推奨コミットメッセージ：
git commit -m "docs(<フォルダー名>): <変更内容の要約>"
```
