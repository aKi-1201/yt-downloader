# YouTube Downloader Notebook 工具集

這個專案提供兩個 Jupyter Notebook：
- 音樂下載：下載 YouTube 音訊為 Opus，支援片段剪輯、封面嵌入、固定 Metadata 與 ReplayGain 標籤。
- 影片下載：下載 YouTube 影片（最高 1080p），輸出 MP4，並嘗試嵌入縮圖與 Metadata。

## 專案結構
- [yt-music-downloader.ipynb](yt-music-downloader.ipynb)
- [yt-video-downloader.ipynb](yt-video-downloader.ipynb)
- [套件.txt](套件.txt)

## 功能總覽

### 1) 音樂下載 Notebook
[yt-music-downloader.ipynb](yt-music-downloader.ipynb)

主要流程：
1. 下載並轉檔為 Opus
2. 可選擇剪輯時間區間
3. 重新寫入 metadata + 封面 + ReplayGain

目前會固定寫入以下 metadata：
- album = YouTube
- albumartist = 空字串
- album artist = 空字串
- date = 2026
- compilation = 1

另外會嘗試加入 ReplayGain：
- replaygain_reference_loudness
- replaygain_track_gain
- replaygain_track_peak
- replaygain_album_gain
- replaygain_album_peak

若 ffmpeg 分析失敗，會回退預設值，下載流程不會中斷。

### 2) 影片下載 Notebook
[yt-video-downloader.ipynb](yt-video-downloader.ipynb)

主要流程：
1. 優先抓取 <= 1080p 最佳畫質
2. 合併輸出為 MP4
3. 寫入 metadata
4. 將縮圖轉 JPG 後嘗試嵌入

## 環境需求

請先安裝：
- Python 3.10+
- ffmpeg（需在 PATH 中）
- JS runtime（必要）：deno（推薦）或 Node.js >= 22

Python 套件：
- yt-dlp[default]（包含 yt-dlp-ejs）
- mutagen

你可以參考 [套件.txt](套件.txt)，或用以下指令安裝：

```bash
pip install -U "yt-dlp[default]" mutagen
```

## 使用方式

### A. 使用 Jupyter Notebook（建議）
1. 在 VS Code 開啟 [yt-music-downloader.ipynb](yt-music-downloader.ipynb) 或 [yt-video-downloader.ipynb](yt-video-downloader.ipynb)
2. 執行第 1 個程式碼 Cell
3. 依提示輸入 YouTube URL
4. 音樂下載模式可選擇是否只下載片段，時間格式支援：
   - 秒（例如 95）
   - MM:SS（例如 01:35）
   - HH:MM:SS（例如 00:01:35）

### B. 以 Python 腳本方式執行（將 Cell 內容另存為 .py 後）
```bash
python your_script.py
```

## 常見問題

### 1) 出現 YouTube JS runtime 警告
自 yt-dlp 2025.11.12 起必須有外部 JS runtime 與 yt-dlp-ejs。請：
1. `pip install -U "yt-dlp[default]"`
2. 安裝 deno，或 Node.js >= 22（`node --version` 確認）

Notebook 會自動偵測並設定 `js_runtimes`。

### 2) ffmpeg 找不到
請確認 ffmpeg 已加入 PATH：
```bash
ffmpeg -version
```

### 3) ReplayGain 顯示為預設值
代表 loudnorm 分析沒有成功解析或執行失敗。常見原因：
- ffmpeg 版本差異
- 輸入音訊異常
- 本機 ffmpeg 執行環境問題

即使如此，檔案仍會下載成功，僅 ReplayGain 可能為 fallback 值。

## 注意事項
- 本專案預設單一網址處理（非 playlist）。
- 下載內容請遵守當地法律與平台使用條款。
- 不同播放器對 ReplayGain/Opus 標籤支援程度不同，建議使用支援較完整的播放器驗證。

## 後續可擴充
- 新增批次下載（多網址）
- 增加輸出目錄設定
- 支援可切換 metadata 固定值
- 輸出下載紀錄（CSV/JSON）
