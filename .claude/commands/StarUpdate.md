# StarUpdate.md — README.md 最終更新・★/◎/△バッジ 一括更新エンジン

あなたはトップページ（`README.md`）の鮮度表示だけを専門に担当する更新係である。

このスキルの唯一の仕事は、`README.md` の全テーブルにある **`最終更新` 列（相対時間テキスト）** と **バッジ列（★/◎/△）** を、実行時点の実態に合わせて洗い直すことである。

内容（行の追加・削除・リンク・本文テキスト・ナレッジ化列など）には一切触れない。

---

## 1. 位置づけ

このスキルが唯一の実装場所である。他のスキルはロジックを持たず、このスキルを呼び出すだけにする。

### 1.1 自動的に呼び出す側

- **`make-report.md`**：Report.md の新規作成・追記が終わった直後
- **`publish-report.md`**：9章のREADME確認後
- **`archive-report.md`**：知識ベース目次・ナレッジ化列の更新後
- **`build-report.md`**：上記3スキルを内部で呼ぶため自動的に実行される

### 1.2 単独実行

`/StarUpdate` とだけ入力すれば、他の工程を挟まずに README.md 全体のバッジ・最終更新列だけをその場で洗い直せる。

---

## 2. 対象：README.md のテーブル

| # | セクション | 基準 |
|---|---|---|
| 1 | 出張報告書 | `PUBLISH_SUMMARY.md` の実行日時 |
| 2 | 講演会レポート | git履歴（実質的な最終編集コミット） |
| 3 | Strategy | git履歴 |
| 4 | 知識ベース：技術テーマ | git履歴 |
| 5 | 知識ベース：企業情報 | git履歴 |
| 6 | 知識ベース：トレンド | git履歴 |
| 7 | 知識ベース：アイデア | git履歴 |

全テーブルを毎回洗い直す。テーブル自体が存在しない場合はスキップ（作成はしない）。

---

## 3. バッジ定義

`.claude/commands/Config/report-config.yml` の `publish.badges` を読む。

| 記号 | 意味 |
|:---:|---|
| ★ | 過去24時間以内に実質的な更新があった |
| ◎ | 過去3日以内（24時間超）に実質的な更新があった |
| △ | 過去7日以内（3日超）に実質的な更新があった |
| （空欄） | 7日を超えている、または実質的な編集履歴が無い |

---

## 4. 共通ロジック（git履歴ベース）

### 4.1 機械的コミットの除外

大規模な一括リファクタリングコミットを除外するパターン：

```bash
is_mechanical_commit() {
  local msg="$1"
  case "$msg" in
    refactor:*|"フォルダ整理"|"Folder-Rename"|"一括移動"|"リポジトリ整理") return 0 ;;
    *) return 1 ;;
  esac
}
```

将来また大規模な整理を行う場合、このリストにだけ追記する。他のスキルには追記しない。

100ファイル超を同時変更するコミットも安全側で除外する。

### 4.2 実質的な最終更新コミットを求める

```bash
find_real_last_edit() {
  local f="$1"
  for h in $(git log --follow --format="%H" -- "$f" 2>/dev/null); do
    msg=$(git log -1 --format="%s" "$h")
    is_mechanical_commit "$msg" && continue
    n=$(git show --stat "$h" 2>/dev/null | tail -1 | grep -oE "^[[:space:]]*[0-9]+" | tr -d ' ')
    [ -z "$n" ] && n=1
    if [ "$n" -le 100 ]; then
      git show -s --format="%ct" "$h"
      return
    fi
  done
}
```

### 4.3 未コミット編集分の優先（mtime）

```bash
effective_time() {
  local f="$1"
  if [ -n "$(git status --porcelain -- "$f" 2>/dev/null)" ]; then
    # Windows: PowerShell で mtime を取得
    # stat -f "%m" はmacOS。Windowsでは以下を使う
    powershell.exe -Command "(Get-Item '${f}').LastWriteTimeUtc | Get-Date -UFormat '%s'" 2>/dev/null \
      || git show -s --format="%ct" HEAD 2>/dev/null
    return
  fi
  find_real_last_edit "$f"
}
```

### 4.4 相対時間テキスト

```bash
rel_time() {
  local f="$1"
  local t; t=$(effective_time "$f")
  [ -z "$t" ] && t=$(git log --follow -1 --format="%ct" -- "$f" 2>/dev/null)
  [ -z "$t" ] && { echo "—"; return; }
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

### 4.5 バッジ記号

```bash
badge_for() {
  local f="$1"
  local t; t=$(effective_time "$f")
  [ -z "$t" ] && { echo ""; return; }
  local diff=$(( $(date +%s) - t ))
  if   [ $diff -lt 86400 ];  then echo "★"
  elif [ $diff -lt 259200 ]; then echo "◎"
  elif [ $diff -lt 604800 ]; then echo "△"
  else echo ""
  fi
}
```

---

## 5. テーブル別の適用手順

### 5.1 出張報告書テーブル（PUBLISH_SUMMARY.md基準）

各レポートフォルダの `PUBLISH_SUMMARY.md` を参照する。

```bash
today=$(date +%s)
for dir in Reports/*/; do
  f="${dir}PUBLISH_SUMMARY.md"
  [ -f "$f" ] || continue
  d=$(grep -A1 "## 実行日時" "$f" 2>/dev/null | tail -1 | grep -oE "[0-9]{4}-[0-9]{2}-[0-9]{2}" | head -1)
  [ -z "$d" ] && continue
  # Windows: date -j は使えない。PowerShell で変換
  t=$(powershell.exe -Command "[int][double]::Parse((Get-Date '${d}').ToUniversalTime().Subtract([datetime]'1970-01-01').TotalSeconds)" 2>/dev/null)
  [ -z "$t" ] && continue
  days=$(( (today - t) / 86400 ))
  echo "$dir : ${days}日前"
done
```

`PUBLISH_SUMMARY.md` が存在しない場合 → `—` / 空欄。

### 5.2 講演会レポート・Strategyテーブル

セクション4の `find_real_last_edit()` / `rel_time()` / `badge_for()` を使う。

```bash
extract_section_links() {
  awk -v p="$1" '
    $0 ~ p {flag=1; next}
    flag && /^#{2,3} / {exit}
    flag
  ' README.md | grep -oE '\]\([^)]+\.md\)' | sed -E 's/^\]\(([^)]+)\)$/\1/'
}
```

### 5.3 知識ベース4テーブル

`extract_section_links` で対象ファイルを抽出し、セクション4のロジックで判定する。

---

## 6. 書き換えルール（冪等性・安全性）

- 各テーブルの各行について、**先頭2列（`最終更新`・バッジ）だけ**を新しい値に置き換える
- 行の追加・削除・並べ替えは行わない
- テーブル自体が存在しない場合はスキップ（新規作成しない）
- 各テーブル直前の凡例キャプション行の `**YYYY-MM-DD HH:MM**` を実行時刻に更新する
- 同じ入力に対して再実行しても、値が変わらない行は変更しない

---

## 7. Windows 環境の注意事項

このリポジトリは Windows で動作する。以下の読み替えを行う：

- `stat -f "%m"` → PowerShell: `(Get-Item $f).LastWriteTimeUtc` からUNIX秒計算
- `date -j -f "%Y-%m-%d"` → PowerShell: `[datetime]::ParseExact($d, 'yyyy-MM-dd', $null)` で変換
- Bash ツールが使える場合は Bash を優先し、PowerShell は補助的に使う

---

## 8. 実行後の報告

```text
StarUpdate による README.md 表示更新を完了しました。

洗い直したテーブル：X個
基準時刻：YYYY-MM-DD HH:MM

変化のあった行：
- 出張報告書：X行（例：202607-TianyuLux → Today / ★）
- ...（変化がなければ「変化なし」）
```
