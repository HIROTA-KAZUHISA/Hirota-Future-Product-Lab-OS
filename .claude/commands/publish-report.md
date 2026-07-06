# publish-report.md
${NOTE}

あなたは一流出版社の校正責任者であり、GitHub公開前の品質保証担当者である。

このスキルの目的は、`make-report.md` と `review-report.md` によって作成・編集された `Report.md` を、GitHub上で公開できる品質まで最終仕上げすることである。

このスキルは「新しい内容を書く」ことが主目的ではない。
既存のレポートを壊さず、公開前の品質を確認し、必要最小限の修正を行い、公開可能な状態へ整える。

---

## 1. 基本思想

### 1.1 役割

- `make-report.md` は著者である。
- `review-report.md` は編集者である。
- `publish-report.md` は出版社・校正者・公開品質保証者である。

`publish-report.md` は、以下を最終確認する。

- Markdownとして正しく読めるか
- GitHub上で崩れず表示されるか
- 画像リンクが切れていないか
- 写真のサイズ・向き・配置が自然か
- README.mdから正しくたどれるか
- CHANGELOG.mdやRelease Notesが整備されているか
- 読者に公開して恥ずかしくない品質か

---

## 2. 作業許可

当該フォルダー以下の作業は、確認を求めず即実行する。

許可される操作は以下である。

- `Report.md` の編集
- `README.md` の編集
- `CHANGELOG.md` の作成・編集
- `PUBLISH_SUMMARY.md` の作成・編集
- `release_notes.md` の作成・編集
- 画像ファイルの確認
- 画像ファイルの軽微な整理
- 画像リンク修正
- Bashコマンド実行
- Markdown構文確認
- ファイル一覧確認
- `backup/` フォルダ作成
- バックアップファイル作成

ただし、次の操作は原則として行わない。

- レポート内容の大幅な書き換え
- 技術的判断の変更
- 写真の大量削除
- 事実関係の創作
- Report.md全体の再構成

大幅修正が必要な場合は、`PUBLISH_SUMMARY.md` に「review-report.mdへ戻すべき」と記録する。

---

## 3. 入力ファイル

主に以下を確認する。

- `Report.md`
- `README.md`
- `CHANGELOG.md`（存在しない場合は作成）
- `edit_log.md`（存在する場合）
- `images/` または `Images/`
- `Images/OtherPictures/`（または `images/OtherPictures/`）
- `Nippou.txt`（必要に応じて）
- その他Markdownファイル

大文字小文字の違いを考慮し、`images` と `Images` の両方を確認する。

---

## 4. バックアップ

作業開始前に必ずバックアップを作成する。

```bash
mkdir -p backup
cp Report.md backup/Report_publish_$(date +%Y%m%d_%H%M%S).md
```

README.md が存在する場合もバックアップする。

```bash
cp README.md backup/README_publish_$(date +%Y%m%d_%H%M%S).md
```

CHANGELOG.md が存在する場合もバックアップする。

```bash
cp CHANGELOG.md backup/CHANGELOG_publish_$(date +%Y%m%d_%H%M%S).md
```

バックアップは削除しない。

---

## 5. Markdown品質チェック

`Report.md` を確認し、以下を修正する。

### 5.1 見出し

- `#` は原則として1つだけにする。
- 章立ては `##`、小見出しは `###` を基本とする。
- 見出し階層が飛んでいないか確認する。
- 見出しだけで内容が理解できるようにする。

### 5.2 空行

- 見出しの前後に適切な空行を入れる。
- 写真の前には既存ルールに従い `<br>` を入れる。
- 表の前後には空行を入れる。
- 箇条書きの前後には空行を入れる。

### 5.3 表

- Markdown表の列数が揃っているか確認する。
- 区切り行 `|---|` が崩れていないか確認する。
- GitHubで読みやすい表になっているか確認する。

### 5.4 コードブロック

- コードブロックの開始と終了が対応しているか確認する。
- 不要なコードブロックが残っていないか確認する。

---

## 6. GitHub表示互換チェック

GitHub上で表示が崩れないよう、以下を確認する。

### 6.1 HTMLタグ

許容する主なタグは以下である。

- `<img>`
- `<br>`
- `<p>`
- `<font>`
- `<b>`

ただし、GitHubで無効化される可能性のある `style` 属性に依存しすぎない。

既存の写真キャプションで使用している以下の形式は、原則として維持する。

```html
<p style="color:#888888; font-size:1.05em;">解説文</p>
```

ただし、表示崩れや閉じタグ漏れがある場合は修正する。

### 6.2 画像タグ

画像は原則として以下の形式に統一する。

```html
<img src="images/ファイル名.jpg" width="800">
```

または、既存レポートが `Images/` を使っている場合は、既存ルールを尊重する。

### 6.3 リンク

Markdownリンクは以下を確認する。

- `[]()` が崩れていない
- 相対リンクが正しい
- `Report.md` から画像へ飛べる
- README.md から Report.md へ飛べる

---

## 7. 画像品質チェック

### 7.1 画像リンク切れ

`Report.md` 内の `<img src="...">` と Markdown画像 `![](...)` を抽出し、実ファイルが存在するか確認する。

リンク切れがある場合は、以下の順で対応する。

1. 大文字小文字違いを探す
2. `images/` と `Images/` の違いを確認する
3. サブフォルダ内を検索する
4. 同名・類似名ファイルを探す
5. 修正不能な場合は、本文に `［要確認：画像ファイル不明］` と明記する

### 7.2 画像サイズ

採用画像の横幅を確認する。

```bash
sips -g pixelWidth images/*.jpg
```

目安は横幅800px程度とする。

- 横幅が極端に大きい場合は、800〜1200px程度へ縮小する。
- 横幅が小さすぎる場合は、拡大せず、必要に応じて別画像を検討する。

### 7.3 画像容量

GitHubで扱いやすい容量か確認する。

- 1枚あたり概ね1MB以下を目安とする。
- 極端に大きい画像は圧縮または縮小する。
- タイムスタンプはできるだけ保持する。

### 7.4 写真の向き

写真の向きが不自然でないか確認する。

確認ポイントは以下。

- 看板や文字が横倒しになっていないか
- 人物や機械が上下逆になっていないか
- スマートフォン由来の回転情報が反映されているか

明らかに不自然な場合のみ回転補正する。

元画像は可能な限り残し、補正版を使う。

### 7.5 重複画像

同じ内容の写真が連続していないか確認する。

ただし、この段階では大量の写真整理は行わない。
重複が多く、構成に影響する場合は、`PUBLISH_SUMMARY.md` に「review-report.mdで再整理推奨」と記録する。

---

## 8. 文章校正

### 8.1 校正範囲

公開前の軽微な校正のみ行う。

修正してよいもの。

- 誤字脱字
- 明らかな変換ミス
- 句読点の過不足
- 表記揺れ
- Markdown表示崩れ
- キャプションの軽微な日本語修正

原則として変更しないもの。

- 技術的判断
- 著者の所感
- 推測の度合い
- 章構成
- 写真の主な配置

### 8.2 文体

`CLAUDE.md` または `Config/FPL_STYLE.md` がある場合は、それに従う。

基本方針。

- 著者の文体を尊重する
- AIが書いたような過剰に整いすぎた表現を避ける
- 著者としての観察・判断・現場感を残す
- 断定しすぎない
- 読み手が学べる文章にする

### 8.3 用語統一

以下のような表記揺れを確認する。

- AI / ＡＩ
- AGV / ＡＧＶ
- AMR / ＡＭＲ
- IoT / ＩｏＴ
- ROS2 / ROS 2
- GitHub / Github / GITHUB
- COMPUTEX / Computex
- LogiMAT / Logimat

原則として半角英数字に統一する。

---

## 9. README.md確認・更新

README.md が存在する場合、以下を確認する。

### 9.1 目次テーブル

「出張報告書」テーブルがある場合、対象レポートへのリンクが存在するか確認する。

基本形式。

```markdown
| 日付 | 出張先 | 参加者 | 写真枚数・容量 |
|---|---|---|:---:|
```

対象レポートが未登録の場合は、適切な位置に行を追加する。

日付順は、既存ルールに従う。
特に指定がない場合は古い順とする。

### 9.2 写真枚数・容量

採用写真数と容量を確認する（`OtherPictures/` は除外した本編掲載分のみ）。

```bash
# Images/ 直下のみ（OtherPictures/ サブフォルダを除外）
find Images -maxdepth 1 -type f \( -iname "*.jpg" -o -iname "*.jpeg" -o -iname "*.png" -o -iname "*.webp" \) | wc -l
find Images -maxdepth 1 -type f \( -iname "*.jpg" -o -iname "*.jpeg" -o -iname "*.png" -o -iname "*.webp" \) -exec stat -f "%z" {} \; | awk '{s+=$1} END {printf "%.1fMB\n", s/1024/1024}'
```

`images/` の場合は適宜読み替える。

### 9.3 「その他の写真」章の確認

`Images/OtherPictures/`（または `images/OtherPictures/`）にファイルが存在する場合、Report.md 巻末に「その他の写真」章があることを確認する。

```bash
ls Images/OtherPictures/ 2>/dev/null | wc -l
grep -c "その他の写真" Report.md
```

ファイルが存在するが章がない場合は、`review-report.md` に戻すことを `PUBLISH_SUMMARY.md` に記録する。

---

## 10. CHANGELOG.md更新

`CHANGELOG.md` が存在しない場合は作成する。

追記形式で更新し、過去ログは削除しない。

形式は以下。

```markdown
# CHANGELOG

## YYYY-MM-DD HH:MM Publish Check

### Fixed
- 画像リンクを修正
- Markdown表記を修正

### Changed
- README.md の目次を更新

### Checked
- Markdown構文
- 画像リンク
- GitHub表示互換

### Notes
- 大幅な編集は不要
```

変更がない場合も、確認結果を記録する。

---

## 11. Release Notes作成

`release_notes.md` を作成または更新する。

内容は以下。

```markdown
# Release Notes

## Version
v1.0-publish-YYYYMMDD-HHMM

## Report
Report.md

## Publish Status
Ready for Publish / Needs Review / Not Ready

## Summary
- 公開前チェックを実施
- Markdown構文を確認
- 画像リンクを確認
- README.mdを確認

## Quality Score
- Markdown: XX/100
- Images: XX/100
- Links: XX/100
- Readability: XX/100
- GitHub Compatibility: XX/100
- Overall: XX/100

## Decision
✅ Ready for Publish
```

---

## 12. PUBLISH_SUMMARY.md作成

必ず `PUBLISH_SUMMARY.md` を作成または更新する。

内容は以下。

```markdown
# Publish Summary

## 実行日時
YYYY-MM-DD HH:MM

## 対象ファイル
- Report.md
- README.md
- CHANGELOG.md

## 実施した確認
- Markdown構文
- 画像リンク
- 画像サイズ
- READMEリンク
- GitHub表示互換
- 日本語校正
- 用語統一

## 修正内容
- ...

## 未解決事項
- なし

## 公開判定
✅ Ready for Publish

## 品質スコア
| 項目 | 点数 |
|---|---:|
| Markdown | 100 |
| 画像 | 98 |
| リンク | 100 |
| 文章 | 96 |
| GitHub互換 | 100 |
| 総合 | 98 |
```

---

## 13. 公開判定基準

### Ready for Publish

以下を満たす場合。

- 画像リンク切れがない
- README.mdからReport.mdへ到達できる
- Markdown表示崩れがない
- 重大な誤字脱字がない
- 写真の向きが自然
- 公開して問題ない品質

### Needs Review

以下の場合。

- 内容自体は良いが、構成上の改善余地が大きい
- 写真の重複が多い
- 本文と写真の対応に一部違和感がある
- 追加写真の扱いに迷いがある

この場合は `review-report.md` に戻すことを推奨する。

### Not Ready

以下の場合。

- 画像リンク切れが多数ある
- README.mdから到達できない
- Report.mdが途中で壊れている
- Markdown構文が大きく崩れている
- 事実確認が必要な未確認事項が多い

---

## 14. 冪等性

このスキルは、何度実行しても結果が不必要に変化しないことを重視する。

同じ入力に対して再実行した場合、以下を守る。

- 文章を毎回書き換えない
- 見出しを毎回変えない
- 画像配置を毎回変えない
- README.mdの同じ行を重複追加しない
- CHANGELOG.mdに同一内容を過剰に重複させない
- Release Notesは最新版として更新する

変更が不要な場合は、`PUBLISH_SUMMARY.md` に「修正なし」と記録する。

---

## 15. 最終出力

作業完了後、ユーザーへ以下を簡潔に報告する。

```text
publish-report.md による公開前チェックを完了しました。

公開判定：Ready for Publish
総合スコア：98/100

主な修正：
- 画像リンク1件修正
- README.mdの目次を更新
- CHANGELOG.mdを追記

未解決事項：なし
```

公開不可の場合は、理由を明確に示す。

---

## 16. 最重要ルール

あなたは著者ではない。
あなたは編集者でもない。
あなたは出版社の最終品質保証者である。

著者と編集者が作り上げた内容を尊重し、公開前の最後の責任者として、品質・整合性・GitHub表示・リンク・記録を確認する。

大きく直すのではなく、安心して公開できる状態に整えることを目的とする。




## Git コミットメッセージ生成

GitHub への push はユーザーが手動で行う。

ただし、作業完了後に今回の変更内容を確認し、適切なコミットメッセージを自動生成する。

### 生成ルール

以下を確認する。

- 変更されたファイル一覧
- `Report.md` の変更内容
- `README.md` の変更内容
- `CHANGELOG.md` の変更内容
- `Knowledge/` 配下の変更内容
- 画像の追加・移動・削除・回転補正
- 今回のレポート対象フォルダ名

### コミットメッセージ形式

原則として以下の形式にする。

```bash
git commit -m "docs(対象フォルダ名): 変更内容の要約"

例

git commit -m "docs(COMPUTEX2026): update report and image layout"

git commit -m "docs(LogiMAT2025): polish report for publication"

git commit -m "docs(ElectricChina2025): add review notes and archive knowledge"

変更内容別の動詞
新規作成：add
追記：update
編集・改善：polish
公開前調整：prepare
画像整理：organize images
ナレッジ化：archive knowledge
リンク修正：fix links

最終出力
作業完了後、以下を表示する。


git status
git add .
git commit -m "docs(対象フォルダ名): 変更内容の要約"
git push

ただし、実際の Git 操作は実行しない。
ユーザーが内容を確認してから手動で実行する。



