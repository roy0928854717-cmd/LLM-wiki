# AGENTS.md — 個人研究知識庫（LLM Wiki）協作規範

本文件是本專案的核心配置檔（Schema），參考 Andrej Karpathy 的 **LLM Wiki** 架構設計。所有 AI Agent（如 Claude Code、OpenAI Codex、Cursor、OpenCode、Gemini 等）在本專案中讀取、撰寫或維護內容時，**必須嚴格遵守本規範**。

---

## 1. 核心理念：累積式知識編譯（Compounding Knowledge）

本專案不是傳統的「臨時檢索問答（RAG）」，而是一個**持續累積、高度互連的持久化知識庫（Persistent Wiki）**。
- **一次編譯，持續更新**：當新論文或技術文章加入時，AI 不只是摘要它，而是將關鍵洞見整合進既有的概念頁、模型／方法頁與總覽中，主動更新交叉引用，並標註新舊觀點的演進或矛盾。
- **人機分工**：人類負責挑選高品質原始資料、提出研究問題與審閱方向；AI 負責所有繁瑣的原文歸檔、摘要、分類、雙向連結維護、索引更新與一致性檢查。

---

## 2. 三層式專案架構（Three-Layer Architecture）

```text
llm-research-wiki/
├── AGENTS.md                 # [Schema層] 本規範文件：定義結構、格式與標準作業流程
├── README.md                 # 專案說明與 GitHub Pages / Obsidian 使用指南
├── index.html                # 網頁閱讀器入口（供 GitHub Pages 直接渲染 wiki/ 內容）
├── raw/                      # [原始資料層] 不可變動（Immutable）的原始文獻與素材
│   ├── assets/               # 本地圖片與圖表附件（供離線與穩定引用）
│   └── ...                   # 論文 PDF/Markdown、網頁剪藏、研究文章（存入後 AI 唯讀）
└── wiki/                     # [知識庫層] 完全由 AI 維護的結構化 Markdown 知識庫
    ├── index.md              # 內容導向總目錄（AI 每次新增/修改頁面必更新）
    ├── log.md                # 時間軸操作日誌（Append-only，記錄每次 ingest/query/lint）
    ├── overview.md           # 研究領域全景總覽與核心演進脈絡
    ├── concepts/             # 核心技術與科學概念頁
    ├── papers/               # 重要論文精讀與貢獻拆解頁
    ├── models/               # 代表性模型、訓練架構或理論框架頁
    └── comparisons/          # 橫向比較表與深度問答沉澱頁
```

### 嚴格權限邊界
1. **`raw/` 目錄為唯讀歸檔區（Append-Only Raw Storage）**：
   - 每當人類貼上新文章或提供新文獻（PDF / 網頁文字）時，AI **必須第一時間將原始內容完整保存至 `raw/` 資料夾**（使用小寫英文 kebab-case 命名，如 `raw/author-year-topic.md`；若為 PDF 則同時保留原檔與可檢索的 `.md` 文字版）。
   - 一旦存入 `raw/`，該檔案即成為「單一事實來源（Source of Truth）」，AI **絕對禁止**修改、刪減或覆寫既有的原始文獻內容。
2. **`wiki/` 目錄由 AI 全權維護**：AI 負責建立、重構與維護 `wiki/` 內的所有 Markdown 檔案。

---

## 3. 頁面格式與撰寫規範（Page Conventions）

### 3.1 YAML Frontmatter 標頭
`wiki/` 底下所有內容頁面（`index.md` 與 `log.md` 除外）**必須**包含標準 YAML Frontmatter，以便 Obsidian Dataview 與網頁端解析：

```yaml
---
title: 頁面完整標題
category: concepts | papers | models | comparisons | overview
tags: [標籤1, 標籤2, 標籤3]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources:
  - raw/對應原始檔名.md
status: seed | developing | evergreen
---
```

### 3.2 檔案命名與連結規範（兼顧 Obsidian 與網頁渲染）
- **檔名規範**：一律使用小寫英文與連字號（kebab-case），例如 `muscle-hypertrophy.md`、`retrieval-augmented-generation.md`，避免空格與特殊符號。
- **連結格式（雙軌相容）**：為了同時支援 Obsidian 圖譜檢視與 GitHub Pages 靜態網頁跳轉，引用站內其他頁面時，請優先使用**標準相對路徑 Markdown 連結**，格式為 `[顯示名稱](../資料夾/檔名.md)`（若在同層目錄則為 `[顯示名稱](檔名.md)`）。
- **語言風格**：內文以**繁體中文**撰寫，專業術語採用「繁體中文（英文專有名詞 / 縮寫）」格式，例如：`自主力竭（Volitional Failure）`、`檢索增強生成（Retrieval-Augmented Generation, RAG）`，確保學術精確度與可讀性。

### 3.3 各類頁面標準結構
- **概念頁（`wiki/concepts/`）**：
  1. 一句話核心定義（Callout 區塊）
  2. 技術／生理原理與運作機制
  3. 演進脈絡與關鍵實證發現
  4. 優勢、限制與實務應用建議
  5. 相關論文、比較表與延伸概念（交叉引用）
- **論文頁（`wiki/papers/`）**：
  1. 基本資訊（發表年份、期刊/會議、作者、原始檔案連結）
  2. 核心問題與研究動機（解決了什麼痛點或爭議？）
  3. 方法架構與研究設計（樣本數、分組定義、統計方法）
  4. 實驗結果與關鍵數據（附具體統計數據如 SMD、95% CI、P 值）
  5. 局限性與實務啟示（連結至相關概念與比較頁面）
- **比較與分析頁（`wiki/comparisons/`）**：
  1. 比較背景與評估維度
  2. 結構化 Markdown 比較表
  3. 適用場景建議與關鍵結論

---

## 4. 三大核心作業流程（Core Operations）

### 流程一：Ingest（攝取新資料）
當人類貼上文章、上傳檔案或要求處理新資料時，依序執行以下步驟：
1. **原始資料歸檔**：將原文完整保存至 `raw/` 資料夾中，確保單一事實來源可追溯。
2. **深度閱讀**：完整閱讀 `raw/` 中的目標文件，提取核心論點、方法細節、實驗數據與引用關係。
3. **建立來源專屬頁**：在 `wiki/papers/`（若是論文）或對應分類建立該來源的精讀頁面，並填寫完整 Frontmatter。
4. **知識融合（關鍵步驟）**：檢查既有的 `wiki/concepts/`、`wiki/models/`、`wiki/comparisons/` 與 `wiki/overview.md`，將新知識整合進去：
   - 若涉及重要核心概念且尚未建立概念頁，主動在 `wiki/concepts/` 建立新頁面。
   - 若涉及多組條件或方法的實證對比，在 `wiki/comparisons/` 建立或更新結構化比較表。
   - 若新資料推翻或修正了舊頁面的說法，**必須明確標註演進與差異**（例如：`> **觀點更新（YYYY-MM-DD）**：根據 ... 的研究，...`）。
   - 確保雙向交叉引用完整。
5. **更新索引與日誌**：
   - 在 `wiki/index.md` 對應分類下新增或更新條目與一句話摘要。
   - 在 `wiki/log.md` 最末端追加一筆紀錄。

### 流程二：Query（查詢與知識沉澱）
當人類針對知識庫提問時：
1. **先查索引**：優先讀取 `wiki/index.md` 與 `wiki/overview.md` 定位相關頁面，再深入讀取目標頁面（避免盲目全庫搜尋）。
2. **附帶出處回答**：綜合多個頁面給出結構化回答，並附上站內頁面連結與原始資料依據。
3. **主動沉澱（Compounding）**：若該問答產出了有價值的跨文件比較、架構整理或新洞見，主動將該分析存為 `wiki/comparisons/` 下的新頁面，並同步更新 `wiki/index.md` 與 `wiki/log.md`，讓對話探索直接轉化為永久知識資產。

### 流程三：Lint（知識庫健康檢查）
當人類要求執行健康檢查（例如：「請執行 wiki lint」）時，全面掃描 `wiki/` 並產出體檢報告：
1. **矛盾與過時檢查**：檢查不同頁面之間是否存在相互矛盾的數據或已被新研究推翻的舊結論。
2. **孤島頁面（Orphan Pages）**：找出沒有任何其他頁面連入（Inbound links = 0）的頁面，建議整合或補充連結。
3. **缺漏概念偵測**：找出內文中頻繁提及、具備重要研究價值但尚未建立獨立頁面的概念，建議建立新條目。
4. **索引與連結驗證**：確認 `wiki/index.md` 是否遺漏任何 `wiki/` 內的檔案，並檢查所有相對路徑連結是否有效。

---

## 5. 索引與日誌格式規範（Indexing & Logging）

### `wiki/index.md` 規範
- 作為整個知識庫的導航地圖，**同時也是 `index.html` 網頁側邊欄的自動解析來源**。
- 每個條目必須維持統一的列表格式：
  `- [頁面標題](分類資料夾/檔名.md) — 一句話精簡摘要（更新日期：YYYY-MM-DD）`
- 每次新增、刪除或大幅改寫頁面時，必須同步更新 `wiki/index.md`。

### `wiki/log.md` 規範
- 採用**僅追加（Append-only）**的時間軸紀錄格式，嚴禁修改歷史紀錄。
- 每筆紀錄標題必須嚴格遵守以下前綴格式（方便以 `grep "^## \[" wiki/log.md` 解析）：
  ```markdown
  ## [YYYY-MM-DD] <ingest | query | lint | init> | <簡短標題>
  - **操作說明**：簡述本次處理的原始資料或任務內容。
  - **影響頁面**：列出新增或更新的 `wiki/` 檔案路徑。
  ```
