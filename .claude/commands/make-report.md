# 展示会レポート作成
${NOTE}

## 情報ソースの優先順位
1. Nippou.txt（最優先）
2. その他のMarkdownファイル
3. 写真のタイムスタンプと内容
4. Web検索（補足のみ・ソース必須）

## 作業方針
- **当該フォルダー以下のあらゆる操作は、すべて許可不要で即実行する。確認を求めない。**
  - ファイル操作（作成・編集・削除・移動・ダウンロード）― Edit・Write ツール含む
  - Bashコマンド実行（sipsによるリサイズ、mv、mkdir、curl、**rm** 等）
  - **動画ファイル（.mp4/.MOV/.AVI等）の削除（rm）― サムネイル抽出後の本体削除も確認不要で即実行する**
  - Report.mdの作成・編集
  - README.mdの編集（目次テーブルへの行追加）
  - OtherPicturesフォルダへの移動
  - 画像のダウンロード・解像度確認
- 上記操作でユーザーに確認・許可を求めることは一切しない
- Edit・Write・Bash のいずれのツール呼び出しも、許可プロンプトなしで実行する
- Report.md が既にある場合は、その内容は変更せずに、新たに追加された写真やNippou.txtの内容に基づき追記する
- 追加された写真は、ファイルのタイムスタンプ（`stat` または `ls -lT` で確認）から撮影日時を特定し、既存レポートの時系列の中で正しい位置に挿入する
- 追記する際は、追記ブロックの**先頭**に以下の形式で目印を入れる（`git config user.name` と現在日時を取得して埋める）：

```
{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}
<font color="{COLOR}"><b>▼ 追記（アカウント名 yy/mm/dd）</b></font>
```

- 追記ブロックの**末尾**にも閉じ線を入れる：

```
{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}{EMOJI}
```

- **追記のたびに色を変えること**。既存の追記マーカーを確認し、以下の順番で次の色・絵文字を使う（GitHub は style属性を除去するため、`<font color="">` と絵文字ブロックで色を表現する）：
  1. 🟥 `#e74c3c`（赤）
  2. 🟦 `#2980b9`（青）
  3. 🟩 `#27ae60`（緑）
  4. 🟧 `#e67e22`（オレンジ）
  5. 🟪 `#8e44ad`（紫）
  - 5色使い切ったら赤に戻る

## 不明な点の扱い
- 情報不足の場合、推測・創作で補わない
- 不明箇所は「［要確認：〇〇が不明］」と明記して空欄にする

## 写真の使い方

### 【最重要】写真は必ず目視してから本文に使う

**全写真を Read ツールで1枚ずつ開いて内容を確認してから、章への配置・キャプション記述を行う。**

- ファイル名（IMG_6xxx、IMG_20260423_xxxなど）から写真の内容を推測して本文に使わない
- タイムスタンプだけを根拠に「この時間帯はこの製品を見ていたはずだから」と配置しない
- 写真の内容が工場・製品と無関係（移動中・食事・観光・人物スナップ等）であれば、本文に使わず OtherPictures に移動する

**目視確認の手順：**
```
1. Images/ 以下の全ファイルを ls でリストアップする
2. Read ツールで各写真を開き、実際に映っている内容をメモする
3. 内容を把握してから、章構成に沿って配置先を決める
4. キャプションは「実際に見た内容」を記述する（推測・補完は禁止）
```

カメラ写真（IMG_6xxx）とスマートフォン写真（IMG_20260423_xxx）が同じフォルダに混在することがある。スマートフォン写真は工場内の製品を接写していることが多く、カメラ写真は移動中や観光の写真が混入していることが多い。どちらも必ず目視確認すること。

---

- 写真は `images/` 直下だけでなく、`images/` 以下のサブフォルダーにある場合もある。再帰的に探索すること
- 動画ファイル（.MOV / .MP4 / .AVI 等）がある場合は、最初のフレームをサムネイル画像として抽出し、他の写真と同様に評価・採用する
  - 抽出には `ffmpeg` を使用する（macOS 標準では入っていないため `brew install ffmpeg` で導入）
  - タイムスタンプは動画ファイルのものを引き継ぐ（`touch -r`）
  - **サムネイル抽出後、動画ファイル本体を必ず削除する。`rm` は確認不要・即実行。スキップしない。**
  ```bash
  for f in Images/*.MOV Images/*.MP4 Images/*.mov Images/*.mp4 Images/*.AVI Images/*.avi; do
    [ -f "$f" ] || continue
    base="${f%.*}"
    ffmpeg -i "$f" -vframes 1 -q:v 2 "${base}_thumb.jpg" -y 2>/dev/null
    touch -r "$f" "${base}_thumb.jpg"
    rm "$f"
  done
  ```
  - `ffmpeg` が使えない場合は `qlmanage` で代替する：
  ```bash
  for f in Images/*.MOV Images/*.MP4 Images/*.mov Images/*.mp4; do
    [ -f "$f" ] || continue
    base="${f%.*}"
    qlmanage -t -s 800 -o "$(dirname "$f")/" "$f" 2>/dev/null
    # qlmanage は ${f}.png を出力するので、JPG に変換して PNG を削除
    if [ -f "${f}.png" ]; then
      sips -s format jpeg -s formatOptions 85 "${f}.png" --out "${base}_thumb.jpg" 2>/dev/null
      touch -r "$f" "${base}_thumb.jpg" 2>/dev/null
      rm "${f}.png"
    fi
    rm "$f"
  done
  ```
  - 削除後に動画が残っていないことを必ず確認する：
  ```bash
  find Images -name "*.mp4" -o -name "*.MP4" -o -name "*.MOV" -o -name "*.mov"
  # 何も出力されなければ削除完了
  ```
  - 抽出したサムネイルはリサイズ・採否判断を他の写真と同じフローで処理する
- **全写真を先に** 横幅800ピクセル程度の .jpg に変換する（sips コマンドで処理）。この処理はリサイズ前に unUsed へ移動しないよう、必ず採否判断より先に行う
- sips でリサイズする際は、**ファイルのタイムスタンプを必ず保持する**こと。リサイズ前に `mtime` を記録し、リサイズ後に `touch` で復元する：
  ```bash
  for f in Images/*.JPG Images/*.jpg; do
    ts=$(date -r "$f" +"%Y%m%d%H%M.%S")
    sips -Z 800 "$f"
    touch -t "$ts" "$f"
  done
  ```
- **基本方針：全写真を本文中で採用する。**ブレ・ピンぼけ・画角の重複がない限り、すべての写真を Report.md の本文に配置する
- 本文への採用から外す写真は、以下の2つに厳密に分類する：
  - **よく似た写真**（採用写真と同じ被写体・ほぼ同じ構図で、追加の情報量がほとんどない）→ `Images/OtherPictures/` に移動。Report.md 末尾の「その他の写真」章に表示する
  - **不採用**（ほぼ同一画角で内容が同じ・明らかなピンぼけ・手ブレで内容不明・誤写）→ `Images/unUsed/` に移動。Report.md には一切表示しない
- 判断基準：「採用済み写真と見た目・被写体がほぼ同じか、または明確に失敗写真か」を問う。どちらでもなければ本文採用
- 話の流れに沿って挿入
- 写真1〜2枚＋説明3行以内をセットで配置
- 写真の解説は必要・タイトルは不要
- 写真の直前には必ず `<br>` を1行入れて上側2行分の余白を確保する
- 写真は `<img src="images/ファイル名" width="800">` で挿入し、解説文は写真の下に配置する
- **縦位置写真（pixelHeight > pixelWidth）は width="500" にする**（縦長になりすぎるため）。`sips -g pixelWidth -g pixelHeight` で確認する。横並び2枚組（width="390"）はそのままでよい
- 解説文は本文と区別できるよう、グレー色・小さめフォントで表示する：`<p style="color:#888888; font-size:1.05em;">解説文</p>`

## 章立て
1. サマリー
2. 展示会概要・参加者
3. 視察の目的
4. 背景（環境変化）
5. 内容（タイムスタンプ順・時系列）
6. まとめ
   - 視察全体を通じた気づき・所感
   - 今後の展開・アクション
   - 不明・未確認事項は「［要確認］」と明記

## 文体ルール
- CLAUDE.mdの文体サンプルに従う
- である調・ですます調は使わない

## インターネット情報の使用ルール
- Nippou.txt の中から展示会の名称を特定し、インターネットで開催概要（主催者・会期・会場・規模・テーマ等）を調査して、Report.md の冒頭概要セクションに端的に記す
- 展示会の公式ホームページがある場合は、展示会の内容が最も端的にわかるイメージ図（メインビジュアル・キービジュアル等）を1枚 `images/` フォルダにダウンロードし、Report.md の冒頭概要セクションに表示する
  - ダウンロード後、必ず `sips -g pixelWidth` で解像度を確認する
  - 横幅が **600px 未満** の場合は解像度不足と判断し、別ソース（出展社プレスリリース・業界メディア・Response.jp 等）から同じ展示会のより大きな画像を再検索・再取得する
  - 最終的に横幅 600px 以上の画像を採用すること。公式バナー（ロゴ＋開催情報入り）と会場全景写真の両方が入手できた場合は、2枚とも掲載する
- ソース（URL・サイト名・取得日）を本文中に明記

## 出力
- ファイル名：Report.md
- 保存先：該当サブフォルダーの直下

## 完了後のアクション
- トップページ（`../../README.md`）の「出張報告書」目次テーブルに行を追加する（レポートは `Reports/202xxx/` 配下なので README.md は2階層上）
- テーブルは **4列構成**。日付順（古い順）に挿入する：

```
| 日付 | 出張先 | 参加者 | 写真枚数・容量 |
|---|---|---|:---:|
```

- 追加する行の形式（README.md からの相対パスを使う）：

```
| 20XX年X月X日 | [🇯🇵 日本／展示会名（会場名）](Reports/フォルダ名/Report.md) | 廣田 | XX枚・X.XMB |
```

- 「写真枚数・容量」は、Images フォルダ直下（OtherPictures・unUsed 除く）の採用写真を集計する：

```bash
# 枚数（Images/ 直下のみ。OtherPictures/ と unUsed/ は除外）
find Images -maxdepth 1 -type f \( -iname "*.jpg" -o -iname "*.jpeg" -o -iname "*.png" -o -iname "*.webp" \) | wc -l
# 合計サイズ
find Images -maxdepth 1 -type f \( -iname "*.jpg" -o -iname "*.jpeg" -o -iname "*.png" -o -iname "*.webp" \) \
  -exec stat -f "%z" {} \; | awk '{s+=$1} END {printf "%.1fMB\n", s/1024/1024}'
```

## その他の写真コーナー

Report.md の本文（「まとめ」章）の最後に以下の章を追加する。

### 章の位置

```
## 1. サマリー
...
## 6. まとめ
...
---

## その他の写真
```

### 章のフォーマット

```markdown
---

## その他の写真

本編採用写真とよく似た写真（同じ被写体・ほぼ同じ構図の角度違い等）を収録する。

（写真が存在する場合）
<br>
<p>
<img src="Images/OtherPictures/ファイル名1.jpg" width="390">
<img src="Images/OtherPictures/ファイル名2.jpg" width="390">
</p>

（1枚だけ余る場合は単独で800幅で掲載）
<br>
<img src="Images/OtherPictures/ファイル名.jpg" width="800">

（動画サムネイルが存在する場合は、写真の後に続けて同じフォーマットで掲載する）
```

### ルール

- `Images/OtherPictures/` 内の**全ファイル**を漏れなく掲載する
- `Images/unUsed/` 内のファイルはこの章に**一切表示しない**
- 写真（JPG/PNG）と動画サムネイル（_thumb.jpg）を分けて並べる必要はない。時系列（ファイル名順）で並べてよい
- キャプションは省略可。ただし1行以上のコメントが付けられる場合は `<p style="color:#888888; font-size:1.05em;">` で追加する
- この章は `review-report.md` / `publish-report.md` 実行のたびに OtherPictures の内容と同期して更新する