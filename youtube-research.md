# YouTube Research → Obsidian Note

$ARGUMENTS に渡された YouTube URL を処理して、Obsidian ノートを作成する。
複数URLをスペースまたは改行で区切って渡すことも可能。

## 手順

以下の Python スクリプトを Bash ツールで実行すること。

```python
import urllib.request, re, json, html as html_mod, os, time
import http.cookiejar
import xml.etree.ElementTree as ET

args = """$ARGUMENTS""".strip()
urls = [u.strip() for u in re.split(r'[\s\n]+', args) if u.strip().startswith('http')]

if not urls:
    print("ERROR: YouTube URL が見つかりません。")
    exit(1)

def safe_name(s):
    return re.sub(r'[\\/:*?"<>|]', '', s).strip()

base_path = "/Users/sanonatsumi/Library/Mobile Documents/iCloud~md~obsidian/Documents/index/ContentsMake/YouTube/Research/"

def process(url):
    vid_match = re.search(r'(?:v=|youtu\.be/)([A-Za-z0-9_-]{11})', url)
    if not vid_match:
        print(f"SKIP（video ID取得失敗）: {url}")
        return None
    vid = vid_match.group(1)

    headers = {
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36',
        'Accept-Language': 'ja,en;q=0.9',
    }

    # メタデータ取得
    cj = http.cookiejar.CookieJar()
    opener = urllib.request.build_opener(urllib.request.HTTPCookieProcessor(cj))
    opener.addheaders = list(headers.items())
    with opener.open(f"https://www.youtube.com/watch?v={vid}", timeout=15) as r:
        page_html = r.read().decode('utf-8')

    title   = (re.search(r'"title":"([^"]+)"', page_html) or re.search(r'', '')).group(1) if re.search(r'"title":"([^"]+)"', page_html) else vid
    ch_m    = re.search(r'"channelName":"([^"]+)"', page_html) or re.search(r'"author":"([^"]+)"', page_html)
    channel = ch_m.group(1) if ch_m else "Unknown"
    date_m  = re.search(r'"publishDate":"(\d{4}-\d{2}-\d{2})', page_html)
    published = date_m.group(1) if date_m else "不明"

    print(f"タイトル: {title}")
    print(f"チャンネル: {channel}")
    print(f"投稿日: {published}")

    # 字幕取得（方法1: captionTracks URL）
    transcript = None
    cap_match = re.search(r'"captionTracks":(\[.*?\])', page_html)
    if cap_match:
        try:
            tracks = json.loads(cap_match.group(1))
            ja_track = next((t for t in tracks if t.get('languageCode') == 'ja'), tracks[0] if tracks else None)
            if ja_track:
                cap_url = ja_track['baseUrl'].replace('\\u0026', '&')
                time.sleep(2)
                with opener.open(cap_url, timeout=15) as r2:
                    xml_data = r2.read().decode('utf-8')
                root = ET.fromstring(xml_data)
                texts = [html_mod.unescape(e.text.strip()) for e in root.findall('.//text') if e.text]
                transcript = "\n".join(texts)
                print(f"字幕取得成功（captionTracks）: {len(texts)}行")
        except Exception as e:
            print(f"captionTracks 失敗: {e}")

    # 字幕取得（方法2: youtube_transcript_api）
    if not transcript:
        try:
            from youtube_transcript_api import YouTubeTranscriptApi
            api = YouTubeTranscriptApi()
            available = list(api.list(vid))
            target = next((t for t in available if t.language_code.startswith('ja')), available[0] if available else None)
            if target:
                fetched = target.fetch()
                transcript = "\n".join([s.text for s in fetched])
                print(f"字幕取得成功（youtube_transcript_api）: {target.language_code}")
        except Exception as e:
            print(f"youtube_transcript_api 失敗: {e}")

    if not transcript:
        desc_m = re.search(r'"shortDescription":"((?:[^"\\]|\\.)*)"', page_html)
        desc = desc_m.group(1).replace('\\n', '\n').replace('\\"', '"')[:1000] if desc_m else ""
        transcript = f"字幕取得不可\n\n【動画説明文】\n{desc}"

    # ファイル保存
    folder = os.path.join(base_path, safe_name(channel))
    os.makedirs(folder, exist_ok=True)
    filepath = os.path.join(folder, safe_name(title) + ".md")

    content = f"""---
channel: {channel}
url: https://www.youtube.com/watch?v={vid}
published: {published}
---
## 文字起こし

{transcript}


## 見出し・内容

"""
    with open(filepath, 'w', encoding='utf-8') as f:
        f.write(content)
    print(f"SAVED: {filepath}")
    return filepath

saved_files = []
for i, url in enumerate(urls):
    print(f"\n--- [{i+1}/{len(urls)}] {url} ---")
    result = process(url)
    if result:
        saved_files.append(result)
    if i < len(urls) - 1:
        time.sleep(3)

print(f"\n完了: {len(saved_files)}/{len(urls)} 件保存")
for p in saved_files:
    print(f"  {p}")
```

## スクリプト実行後にやること

1. 保存されたファイルを Read ツールで順番に開く
2. 各ファイルの `## 文字起こし` を読んで内容を理解する
3. 以下の形式で `## 見出し・内容` を埋める：

```markdown
### この動画の概要
- （要旨を箇条書き）

---

### （見出し）
- （内容）

---

### まとめ
| 項目 | 内容 |
|------|------|
```

4. Edit ツールで各ファイルの `## 見出し・内容\n\n` 直後に挿入する
5. 完了を報告する（ファイルパス・タイトル・投稿日を明記）
