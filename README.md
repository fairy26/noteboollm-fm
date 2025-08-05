# noteboollm-fm

## 手順

- [noteboollm-fm](#noteboollm-fm)
  - [手順](#手順)
    - [NotebookLM の音声解説を軽量ファイル化](#notebooklm-の音声解説を軽量ファイル化)
    - [Google Drive に音声ファイルを保存](#google-drive-に音声ファイルを保存)
    - [Google Drive のファイルを公開 URL で取得](#google-drive-のファイルを公開-url-で取得)
    - [そのリンク群で RSS フィードを作成](#そのリンク群で-rss-フィードを作成)
    - [RSS フィードを GitHub Pages でホスティング](#rss-フィードを-github-pages-でホスティング)
    - [AntennaPod で購読する](#antennapod-で購読する)

### NotebookLM の音声解説を軽量ファイル化

- NotebookLM で作成した音声解説の wac ファイルをローカルにダウンロードする
- ダウンロードした wav ファイルを opus ファイルに ffmpeg で変換する

  ```sh
  cd ~/Downloads
  for f in *.wav; do
  ffmpeg -v error -i "$f" -c:a libopus -b:a 16k "${f%.wav}.opus" -y
  done; \
  for i in {1..3}; do
  afplay /System/Library/Sounds/Submarine.aiff;
  done
  ```

### Google Drive に音声ファイルを保存

- Google Drive のフォルダの共有設定を「リンクを知っている全員にアクセス可能」にし、公開フォルダを用意する
- opus ファイルをその Google Drive のフォルダにアップロードする

  ```sh
  mv ~/Downloads/*.opus '~/Library/CloudStorage/GoogleDrive-{Google Accound ID}/マイドライブ/{path/to/folder}'
  ```

### Google Drive のファイルを公開 URL で取得

1. Google Drive で opus ファイルを右クリック → 「リンクを取得」  
   リンクは以下の形式：

   ```sh
   https://drive.google.com/file/d/{ファイルID}/view?usp=sharing
   ```

2. 音声ファイルにアクセスできるダウンロード URL に変換

   ```sh
   https://drive.google.com/uc?export=download&id={ファイルID}
   ```

### そのリンク群で RSS フィードを作成

1. 以下のような RSS ファイル（XML 形式）を作る：

   ```xml feed.xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <rss version="2.0">
   <channel>
       <title>My Feed</title>
       <link>https://example.com/</link>
       <description>Opus audio uploads</description>
       <item>
       <title>タイトル1</title>
       <enclosure url="https://drive.google.com/uc?export=download&id=ファイルID1" length="1234567" type="audio/ogg" />
       <guid>音声解説1</guid>
       <pubDate>Thu, 11 Jul 2024 10:00:00 +0900</pubDate>
       </item>
       <!-- item要素を複数並べる -->
   </channel>
   </rss>
   ```

### RSS フィードを GitHub Pages でホスティング

1. GitHub でリポジトリを作る
2. feed.xml をルートに置く
3. GitHub Pages を有効化し、URL を得る  
   e.g. https://{yourname}.github.io/my-opus-feed/feed.xml

### AntennaPod で購読する

1. AntennaPod を開く
2. 「+」ボタン → 「RSS フィードから追加」
3. 上記の https://{yourname}.github.io/.../feed.xml を入力する
4. 読み込まれた音声を再生する
