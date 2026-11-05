# 技術內容區：陳述對照證據（AC8）

對應 `about/index.html` 的 `#tech` 區塊。每個 `data-claim` 編號至少有一列證據；「行號」是 `about/index.html` 的行，「片段」是該行裡的原文。行號只對照撰寫當下的 `about/index.html`，檔案之後有更動時要重新核對。

這張表沒有列入、也沒有寫進網站的項目（`about/index.html` 目前沒有實作）：Google Apps Script 與 Google 試算表寫入、`no-cors`、`ai-content-summary`、`<details>` 版 FAQ、`requestAnimationFrame` 驅動的呼吸動畫、任何效能數字、「已驗證上線」、「0 Dependencies」。

| ID | 陳述 | 行號 | 片段 |
|---|---|---|---|
| c1 | CSS 內嵌 | 119 | `<style>` |
| c1 | JavaScript 內嵌（沒有 `src`） | 889 | `<script>` |
| c2 | 色彩與字級用 CSS 自訂屬性 | 120 | `:root {` |
| c2 | 深色配色覆蓋同一組變數 | 161 | `:root[data-theme="dark"] {` |
| c3 | 預設跟著系統深淺色 | 916 | `matchMedia('(prefers-color-scheme: dark)')` |
| c3 | 手動選擇用 localStorage 記住 | 927 | `localStorage.setItem('theme'` |
| c4 | 呼吸階段 4-2-4（吸氣 4000、停住 2000、吐氣 4000 毫秒） | 1022 | `['in', '吸氣', 4000, 1], ['hold', '停住', 2000, 1], ['out', '吐氣', 4000, 0.7]` |
| c4 | 階段由 setTimeout 排程 | 1053 | `setTimeout(` |
| c4 | 圓圈放大縮小用 CSS transition | 333 | `transition: transform 4s cubic-bezier` |
| c5 | 預約表單的 action 是 mailto | 786 | `action="mailto:aboutjob10062@gmail.com"` |
| c6 | 捲動淡入用 IntersectionObserver | 981 | `new IntersectionObserver(` |
| c6 | 減少動態時只淡入、不位移 | 452 | `.js [data-reveal] { transform: none; transition: opacity .4s linear; }` |
| c7 | 860px 以下的版面規則 | 472 | `@media (max-width: 860px)` |
| c7 | 導覽收成選單 | 480 | `.nav ul { display: none; }` |
| c7 | 版面改為單欄 | 474 | `.split { display: block; }` |
| c8 | ProfilePage 結構化資料 | 38 | `"@type": "ProfilePage"` |
| c9 | FAQPage 結構化資料 | 78 | `"@type": "FAQPage"` |
| c10 | Open Graph 預覽文字 | 19 | `property="og:description"` |
| c10 | Twitter 卡片預覽文字 | 29 | `name="twitter:description"` |
| c11 | 單一 HTML 檔、樣式內嵌 | 119 | `<style>` |
| c11 | 單一 HTML 檔、腳本內嵌 | 889 | `<script>` |
| c12 | 預約表單用 mailto | 786 | `action="mailto:aboutjob10062@gmail.com"` |
| c13 | 深淺主題用 CSS 自訂屬性 | 120 | `:root {` |
| c13 | 深色主題用 data-theme 屬性切換 | 161 | `:root[data-theme="dark"] {` |
| c14 | 呼吸階段陣列 | 1022 | `['in', '吸氣', 4000, 1], ['hold', '停住', 2000, 1], ['out', '吐氣', 4000, 0.7]` |
| c14 | 階段由 setTimeout 排程 | 1053 | `setTimeout(` |
| c15 | 字型來自 Fontshare | 115 | `api.fontshare.com` |
| c15 | 字型來自 Google Fonts | 118 | `fonts.googleapis.com/css2` |
| c15 | 校園照片是外部連結（維基共享資源） | 610 | `upload.wikimedia.org` |
| c15 | 另一張校園照片是外部連結 | 866 | `upload.wikimedia.org` |
| c15 | 背景音樂是外部連結 | 575 | `img.ai365.fans` |
