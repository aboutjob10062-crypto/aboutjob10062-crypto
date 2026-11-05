# 作品集首頁與個人簡介頁 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 把現有個人網站整份搬到 `about/index.html`，在根目錄做一個新的作品集首頁（左側定位欄加右側七列分類清單），並修正、加入 `llms.txt` 與 `sitemap.xml`。

**Architecture:** 先做純機械搬移並用逐行比對守住「文案不改」，再做首頁，之後才改技術區那一句、重算證據表，最後處理 SEO 檔案與上線驗證。沒有建置步驟：兩頁都是單一 HTML 檔，內嵌 CSS 與 JS。驗證用 Git Bash 指令、Node 腳本（不安裝套件）與瀏覽器窗格；`$TMP` 指暫存資料夾，檢查腳本放那裡，不進 repo。

**Tech Stack:** 靜態 HTML/CSS/JS、Git、Node v24（只用內建模組）、GitHub Pages。

**Spec:** `docs/superpowers/specs/2026-11-05-portfolio-home-and-about-design.md`（前一份：`2026-11-05-portfolio-repo-structure-design.md`）

## Global Constraints

- 網站網址前綴固定為 `https://aboutjob10062-crypto.github.io/aboutjob10062-crypto/`；首頁是 `/`，個人簡介是 `/about/`。
- 以 PR 合併上線，**不直接推 `main`**；合併需要使用者同意。每次 commit 一律用 `git commit --only -- <路徑>`：工作區有 3 個已 staged 的 `assets/` 刪除，不得被帶進去。
- `about/` 的文案不改，只允許：資源路徑、網址、新增的「首頁」連結，以及技術區中「整個網站是一個 HTML 檔」那一句（Task 4）。
- 首頁與 `about/` 都不得出現產品名稱「第一步」或「first-step」。
- 沒有內容的分類列沒有 `href`；有內容的分類連到 `https://github.com/aboutjob10062-crypto/aboutjob10062-crypto/tree/main/<分類>`。
- 首頁只用既有的顏色變數（`--bg`、`--surface`、`--ink`、`--muted`、`--accent`、`--dark`、`--on-dark`），文字對比達 WCAG AA 4.5:1，320px 與 375px 無水平捲軸。
- 工作區既有的未追蹤 `robots.txt` 不刪除、不 commit；不加 `robots.txt`。
- 沒有 `gh`：開 PR、合併都用 GitHub 的 MCP 工具；PR 說明結尾加 `🤖 Generated with [Claude Code](https://claude.com/claude-code)`，commit 結尾加 `Co-Authored-By: Claude Sonnet 5.5 <noreply@anthropic.com>`。

## Review Focus

1. 「首頁」連結讓導覽列在 861–1000px 又壓到照片（先前新增「技術」項目時發生過）→ Task 1 量測 900px。
2. 在首頁選深色後，進 `about/` 沒有跟著變（`localStorage` 的 `theme` 沒共用）→ Task 2 的檢查。
3. 首頁在深色主題下文字對比不足（`--accent` 在深色底只有約 2.7:1）→ Task 3 兩種主題都量。
4. JavaScript 沒有執行時，首頁七列消失或看不到 → Task 2 檢查七列在靜態 HTML 裡。
5. 861–1000px 時導覽的「首頁」被隱藏，使用者回不了首頁 → Task 1 檢查頁尾選單一定有「首頁」。

---

### Task 1: 個人簡介搬到 `about/index.html`

**對應驗收標準：** AC2、AC3、AC4（個人簡介頁部分）　｜　**優先等級：必須**

**Files:**
- Create branch: `home-and-about`（`git switch -c home-and-about origin/main`；已 staged 的刪除與未追蹤的設計文件、本計畫會跟過來）
- Move: `index.html` → `about/index.html`（`git mv`）
- Modify: `about/index.html`
- Commit 先納入：`docs/superpowers/specs/2026-11-05-portfolio-home-and-about-design.md`、本計畫檔

**Interfaces:**
- Produces: `about/index.html` 保有錨點 `#interview`、`#contact`、`#tech`、`#about`；導覽與頁尾各有一個連到 `../` 的「首頁」連結；`localStorage` 的 `theme` 讀取腳本仍在 `<head>`。

- [ ] **Step 1: 建分支並 commit 設計文件與計畫**

Run: `git fetch -q origin && git switch -c home-and-about origin/main && git add docs/superpowers/specs/2026-11-05-portfolio-home-and-about-design.md docs/superpowers/plans/2026-11-05-portfolio-home-and-about.md && git commit --only -m "Add design and plan for the portfolio home and about pages" -- docs/superpowers/specs/2026-11-05-portfolio-home-and-about-design.md docs/superpowers/plans/2026-11-05-portfolio-home-and-about.md`
Expected: 新分支建立；`git show --stat HEAD` 只有這兩個檔案；`git status` 仍顯示 3 個 `assets/` 刪除為 staged。

- [ ] **Step 2: 寫會失敗的檢查（存成 `$TMP/check-about-diff.js`、`$TMP/check-links.js`）**

- `check-about-diff.js`（AC2）：讀 `git show 22478c0:index.html` 與 `about/index.html`，逐行比對；每一個有差異的行，都必須符合允許清單之一：含 `images/willy.jpg`；含 `aboutjob10062-crypto.github.io/aboutjob10062-crypto/`（網址）；JSON-LD 中被移除的 `WebSite` 節點行；含 `nav__home` 或 `href="../"`；`.nav__home` 的 CSS 規則行。Task 4 之後再加入「`這一頁是單一 HTML 檔`」那一行。任何其他差異行 → 印出並失敗。
- `check-links.js`（AC3）：對指定的 HTML 檔，檢查每個非 `http(s):`、非 `mailto:` 的 `href`、`src` 都指向存在的檔案或資料夾（資料夾要有 `index.html`），且每個 `#錨點` 在目標頁都有對應的 `id`。另斷言 `about/index.html` 的 canonical 與 `og:url` 都等於 `.../about/`，`ProfilePage`、`Person`、`FAQPage` 的 `url` 與 `@id` 都以 `.../about/` 開頭，`WebSite` 節點不在這一頁，`isPartOf` 仍指向 `.../#website`。

- [ ] **Step 3: 執行檢查，確認失敗**

Run: `node "$TMP/check-about-diff.js"; node "$TMP/check-links.js" about/index.html`
Expected: 兩者都 FAIL，原因是 `about/index.html` 不存在。

- [ ] **Step 4: 機械搬移並改路徑與網址**

`git mv index.html about/index.html`，然後只做這些修改：兩張 `<img src="about/images/willy.jpg">` 改為 `images/willy.jpg`；`canonical` 與 `og:url` 改為 `.../about/`；JSON-LD 中刪掉 `WebSite` 節點，`ProfilePage`、`Person`、`FAQPage` 的 `@id` 與 `url` 改用 `.../about/` 前綴（`#profile`、`#person`、`#faq`），`isPartOf` 維持指向 `.../#website`；`og:image`、`twitter:image`、`image` 本來就是絕對網址，不動。

- [ ] **Step 5: 加「首頁」連結並避免導覽壓到照片**

頂部導覽 `<ul id="nav-list">` 最前面加 `<li class="nav__home"><a href="../">首頁</a></li>`；既有的 `@media (min-width: 861px) and (max-width: 1000px)` 規則改為同時隱藏 `.nav__home` 與 `.nav__tech`；頁尾 `<nav aria-label="頁尾選單">` 的 `<ul>` 最前面加 `<li><a href="../">首頁</a></li>`（永遠顯示）。

- [ ] **Step 6: 執行檢查與導覽量測**

Run: `node "$TMP/check-about-diff.js" && node "$TMP/check-links.js" about/index.html`
Expected: 兩者 PASS。
先照 Task 3 Step 2 啟動本機伺服器（之後各任務共用，到 Task 3 Step 4 才關），再在瀏覽器窗格 900px 寬度量 `.nav` 的 `getBoundingClientRect().left >= .stage` 的右緣 − 1；Expected: `true`（Review Focus 1）。並確認頁尾選單的第一項是連到 `../` 的「首頁」（Review Focus 5）。

- [ ] **Step 7: Commit**

```bash
git add about/index.html index.html
git commit --only -m "Move the personal site to about/ and link back to the home page" -- about/index.html index.html
```
用 `git show --stat HEAD` 確認是一筆改名加修改，沒有 `assets/` 的檔案。

---

### Task 2: 新首頁（左側定位欄加七列分類清單）

**對應驗收標準：** AC1、AC3、AC4（首頁部分）、AC6、AC10　｜　**優先等級：必須**

**Files:**
- Create: `index.html`

**Interfaces:**
- Consumes: Task 1 的 `about/index.html` 錨點 `#interview`、`#contact`；`about/images/willy.jpg`。
- Produces: 根目錄 `index.html`，`<ul class="cats">` 含七個 `<li class="cat" data-cat="…">`，每個 `<li>` 內恰有一個 `.cat__name`、一個 `.cat__desc`、一個 `.cat__state`；有連結的列用 `<a class="cat__row" href="…">`，沒有的用 `<div class="cat__row">`。

- [ ] **Step 1: 寫會失敗的檢查（存成 `$TMP/check-home.js`）**

斷言（AC1、AC4、AC6、AC10 與 Review Focus 2、4）：
- 七個 `li.cat` 的 `data-cat` 依序為 `about, apps, games, tools, art, music, videos`；每個恰有一個 `.cat__name`、`.cat__desc`、`.cat__state`；狀態文字各只出現一次，且分別是：`about` → `可以看 →`、`apps` → `1 件製作中 →`、其餘五列 → `即將上線`。
- `about` 列的 `href` 是 `about/`；`apps` 列的 `href` 是 `https://github.com/aboutjob10062-crypto/aboutjob10062-crypto/tree/main/apps` 且含 `rel="noopener"`；其餘五列是 `<div>`，整列沒有 `href`。`apps/` 底下的作品子資料夾數量（`ls -d apps/*/`）等於狀態文字裡的件數。
- 首頁的按鈕：`預約面試` → `about/#interview`，`聯絡我` → `about/#contact`。
- `<title>` 是 `廖煒立 Willy Liao｜作品集首頁` 且不等於 `about/index.html` 的 `<title>`；`<meta name="description">` 是 `廖煒立（Willy Liao）的作品集：個人簡介、網頁應用、互動遊戲、實用工具、數位藝術、音樂創作與影音作品。`；canonical 與 `og:url` 為 `https://aboutjob10062-crypto.github.io/aboutjob10062-crypto/`；`og:image` 為 `.../about/images/willy.jpg`；JSON-LD 含 `WebSite`（`@id` 為 `.../#website`）。
- 檔案不含「第一步」與「first-step」。
- 靜態 HTML 裡就有七列（沒有靠 JS 產生）；`<head>` 含讀取 `localStorage` 的 `theme` 並設定 `document.documentElement.dataset.theme` 的行內腳本，與 `about/index.html` 同一份寫法（共用主題）。

- [ ] **Step 2: 執行檢查，確認失敗**

Run: `node "$TMP/check-home.js"`
Expected: FAIL，原因是根目錄沒有 `index.html`。

- [ ] **Step 3: 建立 `index.html`**

沿用 `about/index.html` 的 `<head>` 基礎（`lang="zh-Hant-TW"`、viewport、`color-scheme`、Fontshare 與 Google Fonts 連結、`:root` 顏色與字級變數、深色配色的三段規則、主題讀取腳本）。左側 `<aside class="home__panel">`：大頭照 `<img src="about/images/willy.jpg" width="800" height="800" alt="廖煒立（Willy Liao）的大頭照：穿深灰色西裝外套與深藍色襯衫，站在灰色背景前">`、`<h1>廖煒立 <span lang="en">Willy Liao</span></h1>`、一句 `游走商業策略與技術實作之間的產品人`、兩個按鈕（`.btn` 與 `.btn--ghost`）；背景用 `var(--dark)`、文字用 `var(--on-dark)`，桌面版 `position: sticky; top: 0; height: 100svh`。右側 `<main>` 放 `<ul class="cats">`，七列的名稱、簡介、狀態文字如 Step 1；已連結的狀態用 `var(--ink)`，不可點的用 `var(--muted)`（不要用 `--accent`，深色底對比不足）。860px 以下改為 `display: block`，定位欄 `position: static`、`height: auto`。CSS 與 JS 內嵌，不載入任何程式庫。

- [ ] **Step 4: 執行檢查，確認通過**

Run: `node "$TMP/check-home.js" && node "$TMP/check-links.js" index.html`
Expected: `PASS`；連結檢查通過（`apps` 的 GitHub 網址不是本機檔案，略過）。

- [ ] **Step 5: 驗證共用主題（Review Focus 2）與 `apps` 連結（AC6）**

沿用 Task 3 Step 2 啟動的本機伺服器，在瀏覽器窗格對首頁執行 `localStorage.setItem('theme','dark')` 後載入 `/about/`，Expected: `document.documentElement.dataset.theme === 'dark'`。另 Run: `curl -s -o /dev/null -w "%{http_code}" https://github.com/aboutjob10062-crypto/aboutjob10062-crypto/tree/main/apps`，Expected: `200`。

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit --only -m "Add the portfolio home page with a category list" -- index.html
```

---

### Task 3: 兩頁的版面與對比驗證

**對應驗收標準：** AC5　｜　**優先等級：必須**

**Files:** 無（只驗證；發現問題就回到對應任務修正並重跑）

- [ ] **Step 1: 寫對比函式**

在瀏覽器的 `javascript_tool` 內定義 `contrastRatio(el: Element): number`（WCAG 相對亮度；背景沿父層找第一個不透明色），與先前驗證技術區時相同。

- [ ] **Step 2: 啟動只讀的本機靜態伺服器**

Run（背景）: `node -e "…"` 一支只提供本 repo 檔案、能依副檔名回 `Content-Type` 的小伺服器，埠 8765，放在 `$TMP/serve-repo.js`。
Expected: 輸出 `listening 8765`。

- [ ] **Step 3: 量測首頁與 `about/`**

對 `/` 與 `/about/`，各以 375×812 與 320×700 設定視窗，Expected: `document.documentElement.scrollWidth <= innerWidth` 皆為 `true`。再各在 `data-theme` 為 `light` 與 `dark` 時，對首頁所有含文字的元素算 `contrastRatio`，Expected: 最小值 `>= 4.5`（Review Focus 3）。另以 `fetch('/')` 取得原始 HTML，確認七列在不執行 JS 時就存在（Review Focus 4）。

- [ ] **Step 4: 收尾**

用 `resize_window` 的 `desktop` 預設還原視窗，關閉背景伺服器（找出佔用 8765 的 node 行程並結束它）。

---

### Task 4: 技術區改寫與證據表重算

**對應驗收標準：** AC8　｜　**優先等級：應該**

**Files:**
- Modify: `about/index.html`（技術區 `.tech__lead` 那一句）
- Modify: `docs/superpowers/evidence/2026-11-05-tech-section-claims.md`
- Modify: `$TMP/check-tech.js`、`$TMP/check-evidence.js`（改讀 `about/index.html`）、`$TMP/check-about-diff.js`（加入允許那一行）

**Interfaces:**
- Consumes: Task 1 的 `about/index.html`；Task 2 之後行號已定案。
- Produces: 證據表每列 `| cN | 陳述 | 行號 | 片段 |` 的行號指向 `about/index.html`。

- [ ] **Step 1: 寫會失敗的檢查**

在 `check-tech.js` 加斷言：技術區不得含 `整個網站是一個 HTML 檔`，且含 `這一頁是單一 HTML 檔`；兩支腳本的讀檔路徑改為 `about/index.html`。

Run: `node "$TMP/check-tech.js"`
Expected: FAIL，指出技術區還含舊句子。

- [ ] **Step 2: 改寫那一句**

`.tech__lead` 的 `整個網站是一個 HTML 檔，不用 JavaScript 框架，也沒有建置步驟。` 改為 `這一頁是單一 HTML 檔，不用 JavaScript 框架，也沒有建置步驟。`（只說這一頁，因此現有證據列已涵蓋，不必新增陳述）。

- [ ] **Step 3: 重算證據表行號**

對每一列，在 `about/index.html` 找出含「片段」的行，行號取離舊行號最近的一行；證據檔開頭的說明文字把「`index.html`」改為「`about/index.html`」。

- [ ] **Step 4: 執行三項檢查**

Run: `node "$TMP/check-tech.js" && node "$TMP/check-evidence.js" && node "$TMP/check-about-diff.js"`
Expected: 三者皆 `PASS`（證據檢查顯示 31 條陳述）。

- [ ] **Step 5: Commit**

```bash
git add about/index.html docs/superpowers/evidence/2026-11-05-tech-section-claims.md
git commit --only -m "Reword the technical section for a two-page site and repoint the evidence table" -- about/index.html docs/superpowers/evidence/2026-11-05-tech-section-claims.md
```

---

### Task 5: `llms.txt` 與 `sitemap.xml`

**對應驗收標準：** AC7　｜　**優先等級：應該**

**Files:**
- Modify: `llms.txt`（「頁面」段）、`sitemap.xml`（目前為未追蹤檔案）

- [ ] **Step 1: 寫會失敗的檢查（存成 `$TMP/check-seo.js`）**

斷言：`sitemap.xml` 的 `<loc>` 恰為 `https://aboutjob10062-crypto.github.io/aboutjob10062-crypto/` 與 `.../about/` 兩個，且沒有以 `.txt` 結尾的；兩者的 `<lastmod>` 都等於今天（`date +%F`）；`llms.txt` 的「頁面」段含這兩個網址；`llms.txt` 裡 18 個事實關鍵字（860、戀結科技、Caty、Lumi、Moodster、20 多位、3 位企業執行長、第 2、3 名、7 天壓縮到 2 天、6 份、2 個核心使用群體、2 項企業輔導、Bubble Developer、linkedin.com/in/liaowilly、aboutjob10062@gmail.com、github.com/aboutjob10062-crypto、Whisper、Jira）都出現在 `about/index.html`；`git ls-files robots.txt` 沒有輸出。

- [ ] **Step 2: 執行檢查，確認失敗**

Run: `node "$TMP/check-seo.js"`
Expected: FAIL，指出 `sitemap.xml` 含 `llms.txt`、缺 `/about/`。

- [ ] **Step 3: 修改兩個檔案**

`sitemap.xml`：只留首頁與 `about/` 兩個 `<url>`，`lastmod` 用今天。`llms.txt` 的「頁面」段改為兩行：`[作品集首頁](…/)：七個分類與各自的狀態`、`[個人簡介](…/about/)：完整介紹、常見問題與預約面試表單`。其餘段落不動。

- [ ] **Step 4: 執行檢查，確認通過**

Run: `node "$TMP/check-seo.js"`
Expected: `PASS`。

- [ ] **Step 5: Commit**

```bash
git add llms.txt sitemap.xml
git commit --only -m "Add llms.txt and sitemap.xml for the home and about pages" -- llms.txt sitemap.xml
```
`robots.txt` 不加入。

---

### Task 6: 開 PR、取得同意後合併並驗證上線

**對應驗收標準：** AC9（並重跑 AC1–AC10 的靜態檢查）　｜　**優先等級：必須（依賴前面所有任務，排在最後）**

**Files:** 無

- [ ] **Step 1: 全部靜態檢查重跑**

Run: `node "$TMP/check-about-diff.js" && node "$TMP/check-links.js" about/index.html && node "$TMP/check-links.js" index.html && node "$TMP/check-home.js" && node "$TMP/check-tech.js" && node "$TMP/check-evidence.js" && node "$TMP/check-seo.js"`
Expected: 全部 `PASS`。並再做一次 Task 3 Step 3 的量測（只重跑 375px），因為 Task 4 動過 `about/index.html`。

- [ ] **Step 2: 推送並開 PR**

Run: `git push -u origin home-and-about`。用 GitHub MCP 的 `create_pull_request` 開 PR（head `home-and-about`，base `main`），說明分「摘要」「合併後會發生什麼」「沒有包含」「驗證」，結尾加 Claude Code 署名；網址與 `10062` 數字開 PR 前逐字核對。
Expected: 取得 PR 網址。

- [ ] **Step 3: 停下來等使用者同意合併**

告訴使用者 PR 網址與「合併後舊網址 `/` 會從個人簡介變成首頁」，詢問是否合併。**未取得同意前不合併。**

- [ ] **Step 4: 合併後驗證 AC9**

同意後用 `merge_pull_request`（`merge_method: merge`，帶 `expectedHeadSha`）合併。等 Pages 重新建置（`curl -f --retry 8 --retry-delay 10 --retry-all-errors`），Run：
`curl -s -o /dev/null -w "%{http_code}\n" https://aboutjob10062-crypto.github.io/aboutjob10062-crypto/` 與 `…/about/`，並分別取 `<title>`。Expected: 兩者 `200`；首頁標題是 `廖煒立 Willy Liao｜作品集首頁`，`about/` 的標題是 `廖煒立 Willy Liao｜AI 產品經理（AI PM）求職作品集`。另確認 `…/sitemap.xml`、`…/llms.txt`、`…/about/images/willy.jpg` 回 200，私人內容（商業計畫書 PDF）回 404。

---

### Task 7: 首頁的深淺主題切換鈕

**對應驗收標準：** （首頁主題共用，見 Task 2 Step 5）　｜　**優先等級：可以（排在最後）**

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 寫會失敗的檢查**

在 `check-home.js` 加斷言：首頁含 `data-theme-toggle` 按鈕（預設 `hidden`，JS 啟用後才顯示），且腳本沿用 `about/index.html` 同一個 `localStorage` 鍵 `theme`。Run: `node "$TMP/check-home.js"`，Expected: FAIL。

- [ ] **Step 2: 實作**

把 `about/index.html` 裡 `themeBtn` 那一段（`showTheme`、`click` 處理、`systemDark` 的 `change` 監聽）精簡複製到首頁的行內腳本，按鈕放在定位欄右上角，樣式沿用 `.toggle`。不放音樂鈕。

- [ ] **Step 3: 驗證**

Run: `node "$TMP/check-home.js"`；瀏覽器窗格點按鈕後 `localStorage.theme` 改變，且 `/about/` 跟著變。Expected: PASS。

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit --only -m "Add a light/dark toggle to the home page" -- index.html
```
若此任務在 Task 6 合併之後才做，要另開一個 PR。

---

## 本計畫不包含

- 優先「這次不做」：各分類自己的網頁、`robots.txt`、為 `robots.txt` 另建 `aboutjob10062-crypto.github.io` repo、到 Google Search Console 提交 sitemap（需使用者自己操作）、自動產生首頁分類清單的腳本。
- 優先「可以」：首頁加「精選作品」區。
- 舊網址帶錨點（例如 `/#interview`）的連結落到新首頁時不會自動轉到 `about/`；如果你履歷上貼過這種連結，需要另外處理。
