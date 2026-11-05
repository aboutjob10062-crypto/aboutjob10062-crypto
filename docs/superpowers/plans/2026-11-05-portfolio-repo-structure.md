# 作品集 GitHub 目錄架構 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 驗證已建好的公開展示櫃架構與公私分界，補上首頁 README 的聯絡方式置頂，並在 `index.html` 新增一個「只寫現況」的技術內容區。

**Architecture:** 目錄架構、範本、授權、展示卡已在 commit `2c74e68` 完成，本計畫先用指令驗證它們（必須），再做兩項修改：`README.md` 前段調整，`index.html` 新增一個 `#tech` 區塊並附一張可重複檢查的「陳述 → 行號」證據表。沒有建置步驟，驗證用 Git Bash 指令與 Node 腳本（不安裝任何套件）。計畫裡的 `$TMP` 指 Windows 暫存資料夾，檢查腳本放那裡，不進 repo。

**Tech Stack:** Markdown、靜態 HTML/CSS/JS（單檔 `index.html`，無框架）、Git、Node v24（只用內建模組做檢查）。

**Spec:** `docs/superpowers/specs/2026-11-05-portfolio-repo-structure-design.md`

## Global Constraints

- 只做本機 commit，**不 push、不開 PR**。
- 商業化作品的內容、原始碼、商業計畫書不進這個公開 repo；公開展示卡只放名稱、一句簡介、狀態、分類、「原始碼：不公開」、合作聯絡指引、保留所有權利聲明。
- 作品狀態只有三級：構想／製作中／可 demo。
- 技術內容區只寫 `index.html` 目前實際有的東西；不改網站功能；不寫任何效能數字（AC10）。
- 技術內容區只用現有顏色變數（`--bg`、`--surface`、`--ink`、`--muted`、`--accent`），文字對比達 WCAG AA 一般文字 4.5:1，375px 寬度無水平捲軸。
- 首頁 README：前 25 行含定位句與 Email 或 LinkedIn 連結，`## 作品集` 標題在前 45 行內；保留 `## 聯絡我`（展示卡連結 `README.md#聯絡我` 依賴它）。
- `assets/` 圖片刪除、`llms.txt`、`robots.txt`、`sitemap.xml` 不在本計畫範圍。
- 不使用 PDF 的「0 Dependencies」說法：頁面載入 Fontshare 與 Google Fonts 字型、LinkedIn 大頭照、外部音樂。可以說的是「沒有 JavaScript 框架」。

## Review Focus

1. 技術名詞的長英文字串在 320px 與 375px 寬度撐出水平捲軸 → Task 4 量測兩種寬度。
2. JavaScript 沒有執行時（`.js` class 沒加上），技術區文字應該仍然可見 → Task 4 移除 `.js` 後檢查。
3. 手動強制淺色或深色主題（`data-theme`）下，技術區對比不足 → Task 4 兩種主題都量。
4. 首頁 README 重排後，`#聯絡我` 錨點消失或重複，展示卡連結失效 → Task 2 檢查恰有一個。
5. 之後有人在資料夾執行 `git add .`，把隔離內容帶進去 → Task 1 用 `git add -n .` 試跑。

---

### Task 1: 驗證結構與公私分界並記錄核准

**對應驗收標準：** AC1、AC2、AC3、AC6　｜　**優先等級：必須**

**Files:**
- Modify: `docs/superpowers/specs/2026-11-05-portfolio-repo-structure-design.md`（只改了狀態行與 AC3，已完成，這裡只 commit）
- Create: 本計畫檔

**Interfaces:**
- Produces: 其餘任務假設 `works/`、`templates/`、`LICENSE`、`.gitignore` 已存在且通過本任務檢查。

- [ ] **Step 1: 執行 AC1 檢查**

Run: `cd /c/Users/Play/Documents/GitHub/aboutjob10062-crypto && for f in README.md LICENSE .gitignore index.html works templates; do test -e "$f" || echo MISSING $f; done; ls works | tr '\n' ' '`
Expected: 沒有 `MISSING`；最後一行 `music research web-apps websites `

- [ ] **Step 2: 執行 AC2 檢查與 `git add .` 試跑（Review Focus 5）**

Run: `git ls-files | grep -E '^(\.superpowers|\.remember|\.taste-skill|dev-flow-package)/|(^|/)\.env' | grep -v '\.env\.example$'; git add -n . | grep -E 'superpowers/brainstorm|\.remember|\.taste-skill|dev-flow-package|\.env|docs/first-step'`
Expected: 兩條指令都沒有輸出。

- [ ] **Step 3: 執行 AC3 檢查**

Run: `git ls-files | xargs grep -Il -e "第一步" -e "first-step" | grep -v -E '^(README\.md|\.gitignore|works/research/first-step-lab/README\.md|docs/superpowers/.*\.md)$'; git ls-files docs | grep -v -E '^docs/superpowers/.*\.md$'`
Expected: 沒有輸出。

- [ ] **Step 4: 執行 AC6 檢查與範本段數檢查**

Run: `grep -E '^#+ *(技術架構|環境設定|資安與權限管理|專案結構)' works/research/first-step-lab/README.md; grep -c '原始碼：不公開' works/research/first-step-lab/README.md; grep -c '^#' templates/public-project-README.md templates/private-project-README.md`
Expected: 第一條沒有輸出；第二條 `1`；第三條 `templates/public-project-README.md:9` 與 `templates/private-project-README.md:10`。

- [ ] **Step 5: 任何一項失敗就修正對應檔案再重跑；全部通過後 commit**

```bash
git add docs/superpowers/specs/2026-11-05-portfolio-repo-structure-design.md docs/superpowers/plans/2026-11-05-portfolio-repo-structure.md
git commit --only -m "Approve portfolio structure design and add implementation plan" -- docs/superpowers/specs/2026-11-05-portfolio-repo-structure-design.md docs/superpowers/plans/2026-11-05-portfolio-repo-structure.md
```
Commit message 結尾加 `Co-Authored-By: Claude Sonnet 5.5 <noreply@anthropic.com>`。已 staged 的 `assets/` 刪除不得被帶入：用 `git show --stat HEAD` 確認只有這兩個檔案。

---

### Task 2: 首頁 README 聯絡方式置頂

**對應驗收標準：** AC4、AC5、AC7　｜　**優先等級：應該**

**Files:**
- Modify: `README.md`（置中 `<div align="center">` 區塊內，第 9 行定位句之後）

**Interfaces:**
- Produces: `README.md` 的 `## 聯絡我` 錨點維持唯一；`## 作品集` 區結構不變。

- [ ] **Step 1: 寫出會失敗的 AC7 檢查**

Run: `head -25 README.md | grep -Eq 'mailto:|linkedin\.com/in' && echo AC7-contact PASS || echo AC7-contact FAIL; head -25 README.md | grep -q 'Aspiring AI Product Manager' && echo AC7-pitch PASS || echo AC7-pitch FAIL; n=$(grep -n '^## 作品集' README.md | cut -d: -f1); [ "$n" -le 45 ] && echo AC7-section PASS || echo AC7-section FAIL; grep -c '^## 聯絡我' README.md`
Expected（修改前）: `AC7-contact FAIL`、`AC7-pitch PASS`、`AC7-section PASS`、`1`

- [ ] **Step 2: 把 Email 與 LinkedIn 兩個徽章連結複製到置中區塊的定位句之後**

沿用 `## 聯絡我` 區已有的兩行 `<a href="mailto:…">` 與 `<a href="https://www.linkedin.com/in/…">` 原樣，不刪除底部的 `## 聯絡我` 區。

- [ ] **Step 3: 重跑 Step 1 並加跑 AC4/AC5 檢查**

Run Step 1 的指令，再執行：
```bash
node -e "
const s=require('fs').readFileSync('README.md','utf8').replace(/<!--[\s\S]*?-->/g,'');
const L=s.split(/\r?\n/);const a=L.findIndex(l=>/^## 作品集/.test(l));
const b=L.findIndex((l,i)=>i>a&&/^## /.test(l));const sec=L.slice(a,b<0?undefined:b);
let zone=null;const bad=[];
for(const l of sec){if(/^### /.test(l))zone=/(⭐|🔧|🌱)/.test(l)?l:null;
else if(/^\|/.test(l)&&!/^\| *(作品|---)/.test(l)&&!zone)bad.push(l);}
if(sec.join('\n').includes('作品名'))bad.push('placeholder');
console.log(bad.length?'FAIL '+bad:'AC4/AC5 PASS');process.exit(bad.length?1:0)"
```
Expected: 三項 AC7 全為 `PASS`，`## 聯絡我` 計數為 `1`，最後一行 `AC4/AC5 PASS`。

- [ ] **Step 4: Commit**

```bash
git add README.md
git commit --only -m "Move contact links to the top of the profile README" -- README.md
```

---

### Task 3: `index.html` 新增技術內容區

**對應驗收標準：** AC8、AC9、AC10　｜　**優先等級：應該**

**Files:**
- Modify: `index.html`：①導覽 `<ul id="nav-list">` 在 `#toolbox` 與 `#contact` 之間加 `<li><a href="#tech">技術</a></li>`；②CSS 在 `.tools li p` 規則之後加 `/* ===== Tech ===== */` 區塊；③`@media (max-width: 860px)` 內在 `.tools ul` 規則之後加 `.tech` 的手機規則；④在工具箱 `</section>` 與 `<section class="faq"` 之間加新區塊。

**Interfaces:**
- Produces: `<section class="tech" id="tech" aria-labelledby="tech-title">`，內含 `<h2 id="tech-title" data-reveal>`、三個 `.tech__group`（每個含 `<h3>` 與一段說明）與一個 `<table class="tech__table">`（欄位：模組、採用、常見替代方案、取捨）；每個陳述技術事實的元素都帶 `data-claim="c1"`、`c2`…（依序編號），Task 4 的證據表以此為準。
- 可寫的陳述（均須在 Task 4 找到行號，找不到就刪）：單一 HTML 檔、內嵌 CSS 與 JS、沒有 JS 框架；以 CSS 自訂屬性管理色彩與字級；系統深淺色自動感應加手動切換並用 `localStorage` 記憶；`ProfilePage` 與 `FAQPage` 結構化資料；原生 HTML 加 CSS transition 做的呼吸引導番茄鐘；`mailto:` 預約表單；`IntersectionObserver` 捲動顯現並尊重 `prefers-reduced-motion`；兩個響應式斷點。「4-2-4」節奏只有在 JS 找得到該節奏時才寫。
- 不可寫：Apps Script、Google 試算表、`no-cors`、`ai-content-summary`、`<details>` FAQ、`requestAnimationFrame` 驅動呼吸、任何時間或百分比數字、「已驗證上線」、「0 Dependencies」。

- [ ] **Step 1: 寫出會失敗的檢查腳本並存成 `$TMP/check-tech.js`（不放進 repo）**

斷言：①`index.html` 含 `id="tech"`；②導覽含 `href="#tech"`；③取 `id="tech"` 到下一個 `</section>` 的片段，不含不可寫清單中任何字串，也不符合 `/\d+(\.\d+)?\s*(秒|ms|%)/`；④該片段內每個 `data-claim` 編號唯一。

- [ ] **Step 2: 執行檢查，確認失敗**

Run: `node "$TMP/check-tech.js"`
Expected: FAIL，訊息指出缺少 `id="tech"`。

- [ ] **Step 3: 依 Interfaces 加入導覽項目、CSS 與區塊**

用工具箱區塊現有的 `padding: var(--section) var(--gutter)` 與 `data-reveal` 慣例；`.tech` 設 `overflow-wrap: anywhere`；手機規則讓 `.tech__table` 的 `table, tbody, tr, td` 改為 `display: block`，每個 `td` 前以 `::before` 顯示 `data-label` 欄名，避免水平捲軸。文案用繁體中文，語氣比照 FAQ 區。

- [ ] **Step 4: 執行檢查，確認通過**

Run: `node "$TMP/check-tech.js"`
Expected: `PASS`

- [ ] **Step 5: 暫不 commit**

`index.html` 還含大量既有的未 commit 修改，commit 前須先問使用者（見 Task 4 Step 5）。

---

### Task 4: 證據表與版面驗證

**對應驗收標準：** AC8、AC9、AC10　｜　**優先等級：應該**

**Files:**
- Create: `docs/superpowers/evidence/2026-11-05-tech-section-claims.md`

**Interfaces:**
- Consumes: Task 3 的 `data-claim` 編號。
- Produces: 證據表，每列格式 `| c1 | 陳述 | 行號 | 片段 |`，「片段」是該行裡一小段原文（不含 `|`）。

- [ ] **Step 1: 寫出會失敗的證據檢查**

腳本 `$TMP/check-evidence.js`：讀證據表每一列，斷言 `index.html` 第「行號」行包含「片段」；且技術區所有 `data-claim` 編號與證據表編號完全一致（不多不少）。

Run: `node "$TMP/check-evidence.js"`
Expected: FAIL（證據檔還不存在）。

- [ ] **Step 2: 建立證據表**

逐一替每個 `data-claim` 在 `index.html` 找出對應程式碼的行號與原文片段；找不到對應的陳述，回 Task 3 刪掉那句再重做。

- [ ] **Step 3: 重跑證據檢查**

Run: `node "$TMP/check-evidence.js"`
Expected: `PASS`

- [ ] **Step 4: 在瀏覽器驗證 AC9（含 Review Focus 1–3）**

用 `node -e "require('http').createServer((q,r)=>require('fs').createReadStream('index.html').pipe(r)).listen(8765)"` 在背景啟動，瀏覽器窗格開 `http://localhost:8765`，以 `resize_window` 分別設 375×812 與 320×700，並在 `javascript_tool` 執行：
- `document.documentElement.scrollWidth <= innerWidth` → 兩種寬度都 `true`。
- `contrastRatio(el: Element): number`（自行實作 WCAG 相對亮度；背景沿著父層找第一個不透明色）對 `#tech` 內每個含文字的元素，在 `document.documentElement.dataset.theme` 設為 `'light'` 與 `'dark'` 時都 `>= 4.5`。
- 移除 `document.documentElement.classList.remove('js')` 後，`#tech` 內每個元素的 `getComputedStyle(el).opacity` 都是 `'1'`。

驗證完關閉背景伺服器，並用 `resize_window` 的 `desktop` 預設還原視窗。

- [ ] **Step 5: 回報使用者並詢問 commit 範圍**

Run: `git diff --stat index.html`
把結果與下面這段話交給使用者決定：`index.html` 含約 320 行早先尚未 commit 的修改（預約表單、主題切換、音樂），技術區描述的正是這些程式碼，所以需要一起進版本庫。問他是否整檔 commit；得到同意才執行：

```bash
git add index.html docs/superpowers/evidence/2026-11-05-tech-section-claims.md
git commit --only -m "Add technical architecture section to the personal site" -- index.html docs/superpowers/evidence/2026-11-05-tech-section-claims.md
```
若使用者不同意，只 commit 證據檔，並在回報中說明證據表的行號依賴尚未 commit 的 `index.html`。不 push。

---

## 本計畫不包含

- 優先「可以」：自動產生首頁作品表的腳本、啟用靈感池收合區。
- 優先「這次不做」：實際上傳各作品、私人 repo 遠端設定、音檔上傳、GitHub Pages 部署。
