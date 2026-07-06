# 議事録作成の準備手順

`_TEMPLATE` は雛形。**コピーして使う。編集しない。**

```text
1. このフォルダをコピーして「YYYYMMDD-会議名」にリネームする
   例：Meetings/20260707-製品会議/

2. 以下のいずれか（両方あればベスト）を入れる：
   ・音声ファイル（.m4a / .mp3 / .wav）← スマホ・ICレコーダーの録音そのままでよい
   ・transcript.txt ← Teams / Zoom の文字起こしがあればこちらを優先

3. Memo.txt に出席者と覚えている決定事項を書く（走り書きでよい）

4. Claude Code で実行：
   /make-minutes YYYYMMDD-会議名
```

- 文字起こしはローカルPC内で行われる（音声が外部に送信されることはない）
- 音声ファイルは git にコミットされない（.gitignore で除外済み）
- 決定事項とアクションアイテムは `Meetings/ActionItems.md` に自動集約される
