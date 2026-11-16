# 🧠 LLM Research Wiki（大型語言模型研究知識庫）

本專案採用 Andrej Karpathy 提出的 **LLM Wiki** 設計模式，將 AI Coding Agent（Claude Code、Codex、Cursor 等）作為專職的知識庫維護者，把零散的論文、技術文章與研究筆記，持續編譯成結構化、高度互連的個人知識庫。

## 📁 專案結構

- **`AGENTS.md`**：給 AI Agent 閱讀的專案憲法與作業規範（Schema），定義目錄權責、YAML 標頭格式，以及 `Ingest`（攝取）、`Query`（查詢沉澱）、`Lint`（健康檢查）三大標準流程。
- **`raw/`**：原始資料層（唯讀）。存放論文 Markdown、網頁剪藏與圖表素材（`raw/assets/`），AI 僅能讀取、不可修改。
- **`wiki/`**：知識庫層（由 AI 維護）。包含內容目錄 `index.md`、操作日誌 `log.md`、領域總覽 `overview.md`，以及 `concepts/`、`papers/`、`models/`、`comparisons/` 四大分類知識頁。
- **`index.html`**：零建置成本的靜態網頁閱讀器，可直接透過 GitHub Pages 部署瀏覽整個知識庫。

## 🚀 快速開始

### 1. 本機預覽網頁
由於瀏覽器安全性限制（`fetch` 讀取本地 Markdown 檔案需要 HTTP 協定），在本機測試時請在專案根目錄啟動簡易伺服器：

```bash
python -m http.server 8000
```
接著開啟瀏覽器前往 `http://localhost:8000` 即可瀏覽知識庫網頁。

### 2. 部署至 GitHub Pages
1. 將本專案推送至 GitHub Repository。
2. 進入 Repository 的 **Settings → Pages**。
3. 在 **Build and deployment** 選擇 **Deploy from a branch**，分支選擇 `main`、資料夾選擇 `/ (root)`，點擊 Save 即可獲得公開網址。

### 3. 搭配 AI Agent 擴充知識庫
1. 將新的論文或文章存入 `raw/` 資料夾（例如 `raw/deepseek-r1.md`）。
2. 在 AI Agent 中輸入指令：
   > 「請依照 `AGENTS.md` 的規範，ingest `raw/deepseek-r1.md`。」
3. AI 會自動建立論文頁、更新相關概念頁與總覽，並同步寫入 `wiki/index.md` 與 `wiki/log.md`；推送到 GitHub 後，網頁側邊欄與內容就會自動更新！
