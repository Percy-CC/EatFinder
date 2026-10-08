# PROJECT.md — 食乜好

> 最後更新：2026-10-08
> 此文件是專案的唯一說明來源。任何 AI 或人類改動專案後，都要更新此文件。

## 1. Overview
- 目的：約 15 位朋友使用的午餐 Web App。
- 功能 A（午餐意願）：每日約 12:00–12:30，用戶選「一齊食／今日唔食」，並可選餐廳或「冇所謂」。
- 功能 B（食評）：隨時可為餐廳評 0–5 星及寫食評。
- 功能 C（投票）：約 12:00–13:00 顯示想一齊食的人及其選擇，大家投票；由用戶按鈕截止。
- 用戶：約 15 人，不公開，只把網址給朋友。無登入。
- 使用平台：手機及電腦瀏覽器；iPhone 可「加入主畫面」。目前未做 PWA manifest。
- 介面語言：香港粵語口語。

## 2. Tech Stack
- 前端：單一 `index.html`（HTML + CSS + JavaScript module），無 build step。
- 後端：Firebase Cloud Firestore（Spark 免費方案，不綁定付款方式）。
- SDK：Firebase JS SDK 10.12.0，由 `gstatic.com` CDN 載入。
- 部署：GitHub Pages（公開儲存庫，Branch `main`，資料夾 root）。
- 規則部署：Firebase CLI（`npx firebase-tools deploy --only firestore:rules`）。
- 開發環境：VS Code + GitHub Copilot。

## 3. File Structure
- `index.html`：整個 App。內含 `firebaseConfig`（用戶自行填入，勿在此文件記錄真實值）。
- `firestore.rules`：Firestore Security Rules。
- `firebase.json`：只設定 firestore rules 路徑。
- `.firebaserc`：Firebase Project ID（default）。
- `AGENTS.md`、`.github/copilot-instructions.md`：給 AI 的指引。
- `PROJECT.md`：本文件。

## 4. Data Model（Firestore）
- `members/{autoId}`：`name`（string）、`ts`。用戶名單，用於下拉選單。
- `restaurants/{autoId}`：`name`、`district`（18 區之一）、`gmap`（Google Maps 連結，必填）、`openrice`（選填）、`addedBy`、`ts`。
- `reviews/{autoId}`：`restId`、`rating`（整數 0–5）、`text`、`by`、`editedBy`（選填）、`ts`。
- `days/{YYYY-MM-DD}`：`closed`（boolean）、`winner`（restaurant ID 或 `"any"`）。
- `days/{date}/intents/{成員名字}`：`name`、`joining`（boolean）、`choice`（restaurant ID 或 `"any"`，不參加時為空字串）、`ts`。
- `days/{date}/votes/{成員名字}`：`name`、`restId`、`ts`。
- 日期使用香港時區（Asia/Hong_Kong）的 `YYYY-MM-DD`。日期變更後 App 自動讀取新一天的文件，這就是「每晚重設」的實現方式，不需要排程。

## 5. Features & Logic
- 身分：用戶從頂部下拉選單選自己的名字，名字存在 `localStorage` 的 `me`。可「新增名字」或「剷除名字」（任何人皆可）。
- 意願：以名字作文件 ID，同一人重複儲存會覆蓋。
- 時間：12:00–12:30 只作提示，不強制。
- 投票候選：只限當日有人選過的餐廳，並排除「冇所謂」及已刪除的餐廳。若沒有候選，顯示「所有人都話冇所謂，唔使投票」。
- 投票：每人一票，可改票；按「截止投票」後，由按下的客戶端計票，平手時隨機抽一個，寫入 `days/{date}.winner`。可「重開投票」。
- 餐廳：任何人可新增及刪除。刪除餐廳時，同時批次刪除該餐廳所有食評。地區（18 區）可用作篩選。
- 食評：所有人可見；任何人可修改及刪除任何人的食評。餐廳頁顯示平均星數及食評數。
- 餐廳名稱由用戶手動輸入。Google Maps 短連結無法在瀏覽器內解析名稱（CORS），故不自動讀取。

## 6. Security Rules（現況）
- 無登入，規則對大部分集合開放讀寫，只對餐廳建立及食評星數做格式檢查。
- 用戶已接受此風險。保護方式只有「不公開網址」。
- 「只可刪自己的食評」等權限無法在無登入下於伺服器端強制。

## 7. Decisions（已定案，改動前須先問用戶）
- 無登入。
- 前端單一 HTML，不用 npm 或框架。
- 用 Firebase Firestore（Spark）而非 Apps Script，原因：需要即時更新及較易移交。
- 食評可被所有人修改及刪除。
- 時間限制不強制。
- 每晚重設以日期分文件實現。
- 平手時隨機決定勝出者。
- 推送提醒（11:55）延後，因 Cloud Functions 排程需要 Blaze 付費方案。
- 日後需要可移交：設定集中在 `firebaseConfig`，Firebase 以 Owner 角色移交，GitHub 儲存庫用 Transfer。

## 8. Setup Summary（給新接手者）
1. Firebase Console 建立專案，註冊 Web App，複製 `firebaseConfig` 到 `index.html`。
2. 建立 Firestore（Databases & Storage → Firestore）。
3. `.firebaserc` 填入 Project ID。
4. `npx firebase-tools login`，再 `npx firebase-tools deploy --only firestore:rules`。
5. 推送到 GitHub 公開儲存庫，Settings → Pages → Deploy from a branch → `main` / root。

## 9. Known Limitations
- 任何人可冒名。
- 本機名字可能被 Safari 清除，需重新選擇。
- 規則全開，網址外流即有被亂寫的風險。
- Spark 方案每日讀取上限 5 萬、寫入 2 萬；App 現時載入全部餐廳、食評及名單，食評很多時讀取量會上升。
- 未做 PWA manifest 及離線支援。
- 代碼未經真實環境完整測試（以首次部署結果為準）。
- 沒有自動刪除舊的 `days` 資料。

## 10. TODO / Ideas
- PWA manifest 及圖示（加入主畫面體驗）。
- 只載入最近 30 日食評以降低讀取量。
- 推送提醒（需升級 Blaze，可由用戶關閉）。
- 清理舊 `days` 資料的方法。
- 重複餐廳檢查（以 Google Maps 連結比對）。

## 11. Changelog（最新在最上，每次改動都要新增）
格式：`YYYY-MM-DD | 改動者（AI 名稱或人） | 檔案 | 改了什麼 | 為什麼 | 是否需重新部署`

- 2026-10-08 | AI | 全部檔案 | 初版：名單下拉選單、餐廳新增／刪除（連帶刪除食評）、食評修改／刪除、投票及截止、每日自動重設 | 依用戶需求建立 | 需 push 到 GitHub 及 deploy rules