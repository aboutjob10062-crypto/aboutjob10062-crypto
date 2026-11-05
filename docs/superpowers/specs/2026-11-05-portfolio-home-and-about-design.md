# 作品集首頁與個人簡介頁設計

日期：2026-11-05　｜　狀態：已審查，待使用者核准

接續 [`2026-11-05-portfolio-repo-structure-design.md`](2026-11-05-portfolio-repo-structure-design.md)。該文件把「新首頁」與「搬移個人簡介網頁」列為另案，這份文件處理它們，並一併處理 `llms.txt`、`sitemap.xml`（`robots.txt` 見第 4 節）。

## 1. User Story

- 身為**正在篩選 AI 產品經理候選人的招募人員**，我想要**在首頁一眼看到廖煒立是誰、有哪些分類，並一步進到他的個人簡介或預約面試**，以達成**快速決定要不要安排面試**。
- 身為**考慮與廖煒立合作的創業者或專案夥伴**，我想要**從首頁看到各分類目前有沒有作品、哪些即將上線，並能一步進到個人簡介**，以達成**判斷他在哪些領域有成果、值不值得進一步聯絡**。
- 身為**廖煒立（作品會陸續增加的作者）**，我想要**首頁與個人簡介分開，而且某個分類有新作品時只要改首頁的一列**，以達成**作品變多也不必重做網站**。

## 2. PRD 六欄

| 欄位 | 內容 |
|---|---|
| 目的與效益 | 目前網址 `/` 直接是個人簡介，作品集沒有入口，分類資料夾也沒有對外頁面。新首頁負責「帶人去看對的分類」，個人簡介回到 `about/`，兩者各司其職（對應三句 User Story）。 |
| 範疇 In / Out | **In**：①根目錄 `index.html` 新首頁：左側固定定位欄（照片、姓名、一句話、預約面試與聯絡按鈕）加右側七列分類清單（about、apps、games、tools、art、music、videos）；②現有個人網站整份搬到 `about/index.html`，文案不改，只改路徑、網址，並加上「回首頁」連結；③兩頁的 canonical、`og:url`、JSON-LD 網址；④`llms.txt`、`sitemap.xml` 修正後加入 repo；⑤技術區陳述與證據表跟著搬移更新。<br>**Out**：各分類自己的網頁（每類一個 `index.html`）；實際上傳作品；`robots.txt`（見第 4 節）；為 `robots.txt` 另建 `aboutjob10062-crypto.github.io` repo；到 Google Search Console 提交 sitemap（需使用者自己操作）；自動產生首頁分類清單的腳本。 |
| 使用情境 | 招募人員：公司桌機或手機，從履歷或 LinkedIn 點進來，目的是決定要不要約面試。合作對象：從推薦或搜尋點進來，想看作品與聯絡方式。廖煒立：Windows 桌機，用 Claude Code 維護；作品新增時改首頁一列的狀態文字。（裝置與時機為 Claude 的判斷） |
| 驗收標準 | 見下方 AC1–AC9。 |
| 限制條件 | GitHub Pages 靜態網站，沒有建置步驟，也沒有伺服器程式。公開 repo：商業化作品的內容不得出現在首頁，首頁只能寫分類層級的狀態。`about/` 的文案不改（除網址、路徑與回首頁連結）。GitHub Pages 不會把資料夾的 `README.md` 變成網頁，因此沒有 `index.html` 的分類資料夾點進去會是 404。以 PR 合併上線，不直接推 `main`。時程：沒有固定日期。 |
| 優先順序 | 見下方優先順序表。 |

### 驗收標準

- **AC1**：根目錄 `index.html` 含七個分類列（about、apps、games、tools、art、music、videos），每列有名稱、一句簡介與狀態文字（「可以看」「N 件製作中」「即將上線」其中之一）。各列內容：`about`「個人簡介、學習經歷、專長技能」可以看；`apps`「網頁應用」1 件製作中；`games`「互動遊戲」、`tools`「實用工具」、`art`「數位藝術」、`music`「音樂創作」、`videos`「影音作品」皆為即將上線（簡介取自使用者提供的目錄圖）。
- **AC2**：`about/index.html` 與搬移前的 `index.html`（commit `22478c0` 的版本）逐行比對，差異只在四類：資源路徑（照片、連結）、網址（canonical、`og:*`、JSON-LD）、新增的「回首頁」連結，以及技術區中「整個網站是一個 HTML 檔」那一句的改寫（見 AC8）。搬移要先做成純機械搬移並通過這條檢查，之後才改那一句，最後重算證據表行號。
- **AC3**：首頁與 `about/index.html` 裡所有本機的 `href`、`src` 都指向存在的檔案；`about/index.html` 內的錨點（`#interview`、`#tech` 等）仍存在；首頁的「看個人簡介」連到 `about/`，「預約面試」連到 `about/#interview`。
- **AC4**：首頁 canonical 與 `og:url` 為 `https://aboutjob10062-crypto.github.io/aboutjob10062-crypto/`，含 `WebSite` 結構化資料；`about/index.html` 的 canonical、`og:url` 與 `ProfilePage`、`Person`、`FAQPage` 的 `url` 為 `.../about/`。
- **AC5**：首頁與 `about/index.html` 在 375px 與 320px 寬度都沒有水平捲軸；首頁文字在淺色與深色主題下對比都達 WCAG AA（一般文字 4.5:1）。
- **AC6**：「有內容」定義為分類資料夾內至少有一個作品子資料夾（`apps/` 目前有 1 個，其餘分類為 0 個）。沒有內容的分類列沒有 `href`，狀態文字是「即將上線」；有內容的分類列，狀態寫「N 件製作中」或「可以看」，連結目標回應 200（連結規則見第 6 節）。
- **AC7**：`sitemap.xml` 只列 HTML 頁面（首頁與 `about/`），`lastmod` 為上線當天；`llms.txt` 的「頁面」段列出這兩個網址，其中每個事實仍能在 `about/index.html` 找到；`robots.txt` 不加入版控（工作區既有的未追蹤檔案維持原樣，不刪除、不 commit）。
- **AC8**：`about/index.html` 技術區的每則陳述仍能在該檔找到對應程式碼，證據表的行號指向 `about/index.html`；技術區不再寫「整個網站是一個 HTML 檔」，改成每個頁面各自是單一 HTML 檔。
- **AC9**：部署後 `https://aboutjob10062-crypto.github.io/aboutjob10062-crypto/` 與 `.../about/` 都回 200，且前者的 `<title>` 不再是個人簡介的標題。合併 PR 前由實作者做本機檢查，合併後由 Claude 用指令驗證（合併本身需使用者同意）。
- **AC10**：首頁七列結構完全相同，每列的狀態文字只出現在該列一次；因此新增或修改一個分類的狀態，只需要改該列的標記。

### 優先順序

| 需求 | 對應驗收 | 優先 |
|---|---|---|
| 個人網站搬到 `about/index.html`，路徑與網址正確 | AC2、AC3、AC4 | 必須 |
| 新首頁（左側定位欄加七列分類清單） | AC1、AC5、AC6 | 必須 |
| 首頁 SEO 設定（canonical、`og:*`、`WebSite` 結構化資料） | AC4 | 必須 |
| 首頁在 860px 以下改為上下排列，定位欄不固定 | AC5 | 必須 |
| 部署後兩個網址都正常 | AC9 | 必須 |
| 七列結構一致、改一列即可 | AC10 | 應該 |
| 首頁「聯絡我」按鈕與 `about/` 的「首頁」連結 | AC3 | 應該 |
| `llms.txt`、`sitemap.xml` 修正並加入 | AC7 | 應該 |
| 技術區陳述與證據表跟著更新 | AC8 | 應該 |
| 首頁的深淺主題切換鈕 | — | 可以 |
| 首頁加「精選作品」區 | — | 可以 |
| 分類詳細頁、`robots.txt`、user-site repo、Search Console 提交、自動產生清單腳本 | — | 這次不做 |

## 3. 反目標（Anti-goals）

- 首頁比個人簡介更重、更花：首頁只負責導覽。
- 任何一個連結 404，或點了沒反應又沒說明。
- 商業化作品的內容、名稱以外的細節出現在首頁。
- 為了搬移而改動個人簡介的文案。

## 4. 使用者已決定的事

- 首頁版型選 **B**：左側固定定位欄、右側分類清單（2026-11-05）。
- 個人簡介搬到 `about/index.html`，根目錄 `index.html` 改成新首頁。
- 分類列的連結規則：`about` 連到 `about/`；有內容的分類連到 GitHub 上的資料夾頁（`https://github.com/aboutjob10062-crypto/aboutjob10062-crypto/tree/main/<分類>`）；沒內容的不可點，只顯示「即將上線」（2026-11-05）。
- SEO 檔案「先檢查再加」。檢查結果：`llms.txt` 的事實都能在網站上找到，只需更新頁面段落；`sitemap.xml` 的 `lastmod` 過期、錯把 `llms.txt` 列為頁面、缺 `about/`。

## 5. Claude 的判斷（請檢查）

- **不加 `robots.txt`**：爬蟲只讀主機根目錄的 `https://aboutjob10062-crypto.github.io/robots.txt`（已確認是 404），放在專案路徑底下會被忽略，裡面的 `Sitemap:` 也一樣。預設就是全部允許，`index.html` 已有 `<meta name="robots" content="index, follow, max-image-preview:large">`。
- 首頁的「apps」列顯示「1 件製作中」，首頁本身不寫產品名稱。
- 首頁放深淺主題切換鈕（沿用 `localStorage` 的 `theme`，兩頁共用），不放音樂鈕。
- 首頁文案沿用個人簡介現有的一句話：「游走商業策略與技術實作之間的產品人」。
- 首頁的左側照片用 `about/images/willy.jpg`。
- `about/index.html` 導覽加一個「首頁」連結。
- 首頁左側按鈕：「預約面試」連到 `about/#interview`，「聯絡我」連到 `about/#contact`（兩個錨點在現有網站都存在）。
- 860px 以下，首頁改成定位欄在上、清單在下，定位欄不固定（沿用現有網站的手機規則）。
- 分類列的「有內容」定義為資料夾內至少有一個作品子資料夾；七列的簡介取自使用者的目錄圖，狀態：about 可以看、apps 1 件製作中、其餘五列即將上線。
- 工作區既有的未追蹤 `robots.txt` 不刪除、不 commit，維持原樣。
- 實作順序：先把 `about/index.html` 做成純機械搬移並通過 AC2，再改技術區那一句，最後重算證據表；llms.txt 與 sitemap.xml 在頁面定案後最後處理。

## 6. 未決事項（交給使用者）

1. ~~分類列的連結規則~~ → **已解決**：使用者選「連到 GitHub 資料夾頁」（2026-11-05），見第 4 節。

目前沒有待回答的問題。

## 7. 交給實作者決定

- 首頁的細部排版與間距（延續現有網站的色彩與字型變數）。
- 首頁的 `<title>` 與 `description` 文案（需和個人簡介頁的區分開）。
