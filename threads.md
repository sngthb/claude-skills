# Threads 自動生成スキル

リライト元原稿フォルダの最新.mdファイルを読み込み、Threads原稿ルールに従って9投稿を自動生成し、原稿フォルダに保存→元ファイルを自動削除するワークフロー。

## 定数

- リライト元原稿フォルダ: `~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/リライト元原稿/`
- 原稿フォルダ（出力先）: `~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/原稿/`
- ルールファイル: `~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/Threads原稿ルール.md`

---

## Step 1: 対象ファイルを特定

以下のコマンドで最新のMarkdownファイルを特定：

```bash
ls -t ~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/リライト元原稿/*.md 2>/dev/null | head -1
```

**チェック：** ファイルが見つからない場合は「処理対象ファイルがありません」と報告して終了。

見つかった場合、ファイル名（拡張子なし）を記憶しておく（Step 5で使用）。

---

## Step 2: 元原稿を読む

Readツールで対象ファイルの内容を取得する。

**ファイルパス例：**
```
~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/リライト元原稿/{filename}.md
```

---

## Step 3: Threads生成ルールを読む

Readツールで以下ファイルを開く：
```
~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/Threads原稿ルール.md
```

内容を把握して、Step 4のプロンプトに含める。

---

## Step 4: Threads 9投稿を生成

**現在のモデルをSonnetに指定してください。**
（`/model`コマンドで`Sonnet`を選択、またはプロンプト内で指示）

以下のプロンプトを実行：

```
あなたはプロのコピーライターです。以下の元原稿をもとに、Threads原稿ルールに厳密に従って、9投稿を生成してください。

【元原稿】
{Step 2で読み込んだ内容}

【Threads原稿ルール】
{Step 3で読み込んだルール内容}

【出力形式】
以下の構造で9投稿を出力してください。改行含め、ルールを厳密に守る。

## ネガティブフック（3投稿）

### ①体験談・例え話・具体例
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行なし}

### ②ノウハウ箇条書き
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行あり、箇条書き/番号付き}

### ③最新情報のシェア
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行なし}

## 常識破壊フック（3投稿）

### ①体験談・例え話・具体例
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行なし}

### ②ノウハウ箇条書き
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行あり、箇条書き/番号付き}

### ③最新情報のシェア
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行なし}

## 質問フック（3投稿）

### ①体験談・例え話・具体例
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行なし}

### ②ノウハウ箇条書き
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行あり、箇条書き/番号付き}

### ③最新情報のシェア
親投稿：{フック（4行以内）}
コメント：{500-1000文字、改行なし}

すべて生成し終えたら終了する。
```

**注意：** 9投稿すべてが完成するまで生成を続けてください。途中で止まった場合は、コメント欄の指示で続行を促してください。

---

## Step 5: ファイルを保存

EditツールまたはBashを使い、以下の形式でファイルを作成・保存：

**ファイル名形式：**
```
{元ファイル名（拡張子なし）}_{YYYY-MM-DD}.md
```

**例：**
- 元ファイル：`リール×ChatGPT.md`
- 出力ファイル：`リール×ChatGPT_2026-04-30.md`

**保存先：**
```
~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/原稿/{出力ファイル名}.md
```

**ファイル内容：**
```markdown
---
generated: {実行日時}
source: {元ファイル名}
---

{Step 4で生成した9投稿}
```

---

## Step 6: 元ファイルを削除

Bashで以下コマンドを実行：

```bash
rm ~/Library/Mobile\ Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/リライト元原稿/{元ファイル名}.md
```

**チェック：** 削除成功を確認。

---

## Step 7: 完了報告

以下の情報を報告：

- ✅ 保存したファイルパス
- ✅ 生成した投稿数（9投稿）
- ✅ 元ファイル名
- ✅ 実行時間の目安

**完了メッセージ例：**
```
✅ Threads 9投稿を生成しました

📁 保存先: ~/Library/Mobile Documents/iCloud~md~obsidian/Documents/index/ContentsMake/Threads/原稿/リール×ChatGPT_2026-04-30.md
📝 元ファイル: リール×ChatGPT.md（削除済み）
⏱️ 実行時間: 約3分
```

---

## トラブルシューティング

| 問題 | 対応 |
|------|------|
| ファイルが見つからない | `ls`コマンドで手動確認し、パスが正しいか確認 |
| 生成が途中で止まる | 「続きから生成してください」と促す、または分割実行を検討 |
| 出力ファイルが保存されない | 原稿フォルダの書き込み権限を確認 |
| 元ファイルが削除されない | `rm`コマンドを手動実行して確認 |

---

## 使用例

```
/threads
```

このコマンド1発で、Step 1〜7がすべて自動実行されます。
