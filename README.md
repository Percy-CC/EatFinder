# 食乜好

約 15 位朋友使用的午餐 Web App：每日報名一齊食、揀餐廳、投票，並可為餐廳寫食評。介面語言為香港粵語口語。

## 功能

- **今日午餐**：選「一齊食／今日唔食」，並可揀餐廳或「冇所謂」（建議填寫時間 12:00–12:30，非強制）。
- **投票**：只限當日有人揀過的餐廳；每人一票，可改票；由用戶按「截止投票」，得票最多者勝，平手隨機。若所有人都揀「冇所謂」則不用投票。
- **食評**：0–5 星、每 0.5 星一級及文字評語；所有人可見，並可修改或刪除任何食評；可讚好、按月曆日期篩選及分頁。
- **餐廳清單及地圖**：手動輸入名稱，揀選 18 區之一，需填 Google Maps 連結，OpenRice 連結選填；可輸入地址並自動判斷地區；顯示平均星數及食評數，可按地區篩選、新增及刪除。Leaflet 地圖可按地區或今日候選篩選，顯示有座標的餐廳及用戶位置。
- **名單**：用戶在頂部下拉選單選擇自己的名字，可新增或剷除名字。
- **每晚重設**：資料按香港日期（YYYY-MM-DD）分開儲存，換日後自動使用新一天的資料。
- **外觀**：Apple HIG 風格介面，支援深色／淺色模式。

## 技術

- 前端：單一 `public/index.html`（HTML、CSS、JavaScript module），沒有 build step。
- 資料庫：Firebase Cloud Firestore（Spark 免費方案）。
- Firebase JS SDK 10.12.0（由 CDN 載入）。
- 地圖及連結處理：Leaflet／OpenStreetMap；Apps Script Web App 用於讀取餐廳資料及展開 Google Maps 短連結。
- 部署：Cloudflare Workers Static Assets（`wrangler.jsonc`）；Firestore 規則以 Firebase CLI 獨立部署。

## 檔案說明

| 檔案 | 作用 |
|---|---|
| `public/index.html` | 整個前端 App，內含 `firebaseConfig` |
| `wrangler.jsonc` | Cloudflare Workers Static Assets 部署設定，網站檔案目錄為 `./public` |
| `firestore.rules` | Firestore 安全規則 |
| `firebase.json` | 指示 Firebase CLI 規則檔位置 |
| `.firebaserc` | 指定對應的 Firebase 專案（Project ID） |
| `AGENTS.md` | 給 AI 助手的工作規則 |
| `PROJECT.md` | 完整專案說明、決定事項及 Changelog |

## 安裝及部署

1. 在 Firebase Console 建立專案，註冊 Web App，將 `firebaseConfig` 設定於 `public/index.html`；不要把真實設定值複製到文件。
2. 在 Firebase Console 建立 Firestore 資料庫。
3. 在 `.firebaserc` 填入 Firebase Project ID。
4. 登入 Firebase CLI 並部署 Firestore 規則（此部署與 Cloudflare 網站部署分開）：

   ```
   npx firebase-tools login
   npx firebase-tools deploy --only firestore:rules
   ```

5. 使用 Cloudflare 帳戶透過 Workers Git 整合連結 GitHub 儲存庫。以根目錄 `wrangler.jsonc` 為設定，Build command 留空，Deploy command 設為 `npx wrangler deploy`；`assets.directory` 為 `./public`。
6. Push 到 GitHub `main` 分支後由 Cloudflare 自動部署。網站實際網址（`workers.dev`）待確認，請從 Cloudflare 專案核實後填入或使用。iPhone 可用 Safari「分享 → 加入主畫面」。

## 日常維護

| 情況 | 做法 |
|---|---|
| 修改畫面或功能 | 編輯 `public/index.html`，Commit 並 Push 到 GitHub `main`；Cloudflare Git 整合會自動部署 |
| 修改規則 | 編輯 `firestore.rules`，再執行 `npx firebase-tools deploy --only firestore:rules` |
| 清理垃圾資料 | 在 Firebase Console → Firestore 手動刪除 |
| 檢查用量 | Firebase 用量在 Firebase Console 查看；Cloudflare 用量及免費方案限額以 Cloudflare 官方文件為準。靜態檔案請求免費；若加入 Worker 函數，須另外留意相關限額 |
| 部署設定 | 修改前先閱讀根目錄 `wrangler.jsonc`；不要把網站檔案資料夾以外的檔案放入 `assets.directory` |
| Firebase 網站限制 | 若曾為 Firebase API key 設定 HTTP referrer 限制，加入新的 Cloudflare 網站網址；目前未確認有否設定 |

## 移交

- Firebase：Console → 專案設定 → Users and permissions，加入新成員為 Owner，再移除自己。
- GitHub：將儲存庫轉移給接手者，或按團隊安排授予其所需權限；轉移操作及設定以 GitHub 當前介面為準。
- Cloudflare：將接手者加入 Cloudflare 帳戶／專案，並確認 Workers Git 整合仍連到正確的 GitHub 儲存庫及 `main` 分支。Cloudflare 專案移交及 GitHub 重新授權的確切操作待確認；若原連結不能沿用，需由接手者在其 Cloudflare 帳戶重新連結儲存庫，並核對 `wrangler.jsonc`、空白 Build command 及 `npx wrangler deploy` Deploy command。
- 網站網址：目前實際 `workers.dev` 網址待確認；移交後從 Cloudflare 核實網站網址及部署狀態。
- 設定分屬 `public/index.html` 的 Firebase Web 設定、`.firebaserc` 的 Firebase Project ID，以及 `wrangler.jsonc` 的 Cloudflare 部署設定；更新前先確定各自對應的專案，切勿把金鑰或憑證寫入文件。

## 已知限制

- 沒有登入：任何人都可以選擇他人的名字。
- 規則大致為全開放（只檢查餐廳及星數格式），保護方式只有「不公開網址」。
- 本機儲存的名字可能被 Safari 清除，需重新選擇。
- 食評及餐廳增加後，每次開啟的讀取量會上升，注意免費額度。
- 未有 PWA manifest 及推送提醒（排程提醒需要 Firebase Blaze 付費方案）。
- 網站網址已更改為 Cloudflare Workers；實際 `workers.dev` 網址待確認。若 Firebase API key 有 HTTP referrer 網站限制，須加入新的網站網址，目前未確認有否設定。
- Cloudflare 靜態檔案請求免費；免費方案限額以 Cloudflare 官方文件為準，不在此列未核實的數字。Worker 函數及 Workers AI 尚未實作，日後加入前須核對其限額。

## 安全提醒

- `firebaseConfig`（含 `apiKey`）屬公開識別資料，不是密碼；真正的保護來自 Firestore 規則。
- 切勿把 service account key、`.env` 或 Firebase CLI 登入憑證提交到 GitHub。
- 只把網址分享給信任的朋友。

## 給 AI 助手

開始工作前請先閱讀 `AGENTS.md` 及 `PROJECT.md`，完成後更新 `PROJECT.md` 的 Changelog。
