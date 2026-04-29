# SEO Write

ResearchフォルダのファイルにSEOブログ記事を書き、原稿フォルダに移動する。
記事はWordPress Gutenbergブロック形式（WINGテーマ）で出力し、画像プロンプトも生成する。

## 定数

- Researchフォルダ: `/Users/sanonatsumi/Library/Mobile Documents/iCloud~md~obsidian/Documents/index/ContentsMake/WordPress/Research/`
- 原稿フォルダ: `/Users/sanonatsumi/Library/Mobile Documents/iCloud~md~obsidian/Documents/index/ContentsMake/WordPress/原稿/`
- SEO原稿ルール: `/Users/sanonatsumi/Library/Mobile Documents/iCloud~md~obsidian/Documents/index/ContentsMake/WordPress/SEO原稿ルール.md`

## Step 1: 対象ファイルを特定

$ARGUMENTS にキーワードが指定されていれば `Research/キーワード.md` を対象にする。
指定がなければ Bash で Research フォルダ内の .md ファイルを一覧取得し、
各ファイルを Read で開いて `## 確定見出し（人間が調整）` と次の `##` の間に
テキストが記入されているファイルをすべて対象にする。
対象ファイルが0件なら「確定見出しが入ったファイルがありません」と報告して終了。

## Step 2: SEO原稿ルールを読む

Read ツールで SEO原稿ルール.md を開き内容を把握する。
（ペルソナ・文体・NG表現・カテゴリ・タグ・ライティングポリシーを記事執筆に反映する）

## Step 3: 記事を執筆する（WordPress Gutenbergブロック形式）

Researchファイルを Read で開く。
- `## 確定見出し（人間が調整）` を見出しとして使用
- frontmatterの `keyword:` をキーワードとして使う

SEO原稿ルール.md の条件をすべて守って執筆する。
出力は**WordPress投稿画面のコードエディタにそのまま貼り付けられる形式**にする。

### フォーマットルール（厳守）

**冒頭（リード文）：吹き出し2つ＋導入文**
```
<!-- wp:st-blocks/st-kaiwa {"shortcode":"st-kaiwa4"} -->
[st-kaiwa4]<div class="wp-block-st-blocks-st-kaiwa"><!-- wp:paragraph -->
<p>（読者が感じる悩みや疑問）</p>
<!-- /wp:paragraph --></div>[/st-kaiwa4]
<!-- /wp:st-blocks/st-kaiwa -->

<!-- wp:st-blocks/st-kaiwa {"shortcode":"st-kaiwa5","reverse":true} -->
[st-kaiwa5 r]<div class="wp-block-st-blocks-st-kaiwa"><!-- wp:paragraph -->
<p>（もう一つの悩みや疑問）</p>
<!-- /wp:paragraph --></div>[/st-kaiwa5]
<!-- /wp:st-blocks/st-kaiwa -->

<!-- wp:paragraph -->
<p>そんな疑問をまとめて解決します！</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>（この記事で解決できること・読むメリットの導入文）</p>
<!-- /wp:paragraph -->

<!-- wp:block {"ref":131} /-->
```

**H2見出し**
```
<!-- wp:heading -->
<h2 class="wp-block-heading">見出しテキスト</h2>
<!-- /wp:heading -->
```

※ H2直後の画像はWordPressのブロックエディタで手動挿入するため、コードに含めない。

**H3見出し**
```
<!-- wp:heading {"level":3} -->
<h3 class="wp-block-heading">小見出しテキスト</h3>
<!-- /wp:heading -->
```

**段落**
```
<!-- wp:paragraph -->
<p>テキスト。改行は<br>で入れる。</p>
<!-- /wp:paragraph -->
```

**マーカー（重要キーワード・UI要素名・重要な数字に使う）**
```
<span class="st-mymarker-s">マーカーをつけるテキスト</span>
```
※ spanはpタグの中に埋め込む。多用しない（1段落に1〜2箇所まで）

**番号付き手順リスト**
```
<!-- wp:list {"ordered":true,"className":"is-style-st-maruno"} -->
<ol class="wp-block-list is-style-st-maruno"><!-- wp:list-item -->
<li>手順1</li>
<!-- /wp:list-item -->

<!-- wp:list-item -->
<li>手順2</li>
<!-- /wp:list-item --></ol>
<!-- /wp:list -->
```

**セクション間の区切りブロック（H2セクションの終わり・段落間に挿入）**
```
<!-- wp:block {"ref":131} /-->
```

**太字**
```
<strong>テキスト</strong>
```（pタグ内に埋め込む）

### 使ってはいけない装飾
- `[st-mybox]` 系のボックス
- `[st-cmemo]`
- `[st-button]`
- markdownの `**` `##` などの記法（Gutenbergブロック形式のみ使う）

## Step 4: 画像プロンプトを生成する

アイキャッチ1枚＋各H2セクション1枚ずつの画像プロンプトを生成する。

### プロンプトのルール（共通）
- 日本語と英語の両方で記述
- 登場人物は必ず女性（30代・笑顔・親しみやすい）
- イラスト・図解スタイル（写真調は避ける）
- パステルカラー・明るいトーン
- Canvaマジック生成・DALL-E・Midjourney どのツールでも使える汎用プロンプト

### アイキャッチのルール（個別）
- **記事タイトルのテキストを画像内に入れる**（大きく読みやすく）
- タイトル文字がメインビジュアルとして目立つデザインにする
- 文字の背景はシンプルにしてテキストが読みやすい配色にする

### H2セクション画像のルール（個別）
- **画像内にテキストを入れない**
- そのセクションのテーマに合ったシーン・状況を具体的に描写する
  （例：「フォント選択画面を見ている」「設定パネルを操作している」など操作・場面を具体的に）
- 「パソコンを見ている」だけにならないよう、セクション内容に即した描写にする

### プロンプトフォーマット
```
【アイキャッチ】
日本語：（記事タイトルのテキストを含む。女性30代、イラスト調、パステルカラー）
English：（include the article title text. woman in 30s, illustration style, pastel colors）

【H2①：セクション名】
日本語：（そのセクションテーマに即した具体的なシーン描写。テキストなし）
English：（specific scene matching the section theme. no text）

【H2②：セクション名】
日本語：...
English：...
```

## Step 5: ファイルを作成して移動する

Edit ツールでファイルのfrontmatter直後に以下を挿入する。
**`## 見出し案（自動生成）` `## 上位記事の見出し貼り付け①②③④` のセクションは削除せずそのまま残す。**

```
## 原稿（WordPress投稿用）

\`\`\`html
（Step3で作成したGutenbergブロック形式の記事）
\`\`\`

スラッグ：
メタディスクリプション：
メタキーワード：
カテゴリ：
タグ：

---

## 原稿（コピペ用）

（HTMLタグ・ブロックコメントをすべて除去したプレーンテキスト版。
見出しはH1/H2/H3をそのままテキストで表記。
箇条書きは「・」または「1.」で表記。
NotebookLMや他ツールへのコピペ用。）

---

## 画像プロンプト

（Step4で生成したプロンプト一覧）

---

```

Bash でファイルを Research → 原稿フォルダへ移動する。

## Step 6: 完了報告

- 記事タイトル・スラッグ
- 使用した装飾の種類と箇所数
- 生成した画像プロンプトの枚数
- 保存先パス
