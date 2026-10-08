# 食乜好

約 15 位朋友使用的午餐 Web App：每日報名一齊食、揀餐廳、投票，並可為餐廳寫食評。介面語言為香港粵語口語。

## 功能

- **今日午餐**：選「一齊食／今日唔食」，並可揀餐廳或「冇所謂」（建議填寫時間 12:00–12:30，非強制）。
- **投票**：只限當日有人揀過的餐廳；每人一票，可改票；由用戶按「截止投票」，得票最多者勝，平手隨機。若所有人都揀「冇所謂」則不用投票。
- **食評**：0–5 星及文字評語；所有人可見，並可修改或刪除任何食評。
- **餐廳清單**：手動輸入名稱，揀選 18 區之一，需填 Google Maps 連結，OpenRice 連結選填；顯示平均星數及食評數；可按地區篩選；可新增及刪除。
- **名單**：用戶在頂部下拉選單選擇自己的名字，可新增或剷除名字。
- **每晚重設**：資料按香港日期（YYYY-MM-DD）分開儲存，換日後自動使用新一天的資料。

## 技術

- 前端：單一 `index.html`（HTML、CSS、JavaScript module），沒有 build step。
- 資料庫：Firebase Cloud Firestore（Spark 免費方案）。
- Firebase JS SDK 10.12.0（由 CDN 載入）。
- 部署：GitHub Pages；規則以 Firebase CLI 部署。

## 檔案說明

| 檔案 | 作用 |
|---|---|
| `index.html` | 整個 App，內含 `firebaseConfig` |
| `firestore.rules` | Firestore 安全規則 |
| `firebase.json` | 指示 Firebase CLI 規則檔位置 |
| `.firebaserc` | 指定對應的 Firebase 專案（Project ID） |
| `AGENTS.md` | 給 AI 助手的工作規則 |
| `PROJECT.md` | 完整專案說明、決定事項及 Changelog |

## 安裝及部署

1. 在 Firebase Console 建立專案，註冊 Web App，複製 `firebaseConfig`。
2. 在 Firebase Console 建立 Firestore 資料庫。
3. 把 `firebaseConfig` 貼入 `index.html`；在 `.firebaserc` 填入 Project ID。
4. 登入 Firebase CLI 並部署規則：

   ```
   npx firebase-tools login
   npx firebase-tools deploy --only firestore:rules
   ```

5. 推送到 GitHub 公開儲存庫，於儲存庫 Settings → Pages，Source 選 Deploy from a branch，Branch 選 `main`，資料夾選 `/ (root)`。
6. 開啟 `https://<用戶名>.github.io/<儲存庫名>/`。iPhone 可用 Safari「分享 → 加入主畫面」。

## 日常維護

| 情況 | 做法 |
|---|---|
| 修改畫面或功能 | 編輯 `index.html`，Commit 並 Push |
| 修改規則 | 編輯 `firestore.rules`，再執行 `npx firebase-tools deploy --only firestore:rules` |
| 清理垃圾資料 | 在 Firebase Console → Firestore 手動刪除 |
| 檢查用量 | Firebase Console 的 Usage 頁，Spark 方案每日 5 萬次讀取、2 萬次寫入 |

## 移交

- Firebase：Console → 專案設定 → Users and permissions，加入新成員為 Owner，再移除自己。
- GitHub：儲存庫 Settings 最底的 Danger Zone → Transfer。轉移後 Pages 網址會隨用戶名改變。
- 設定都集中在 `firebaseConfig` 及 `.firebaserc`，換專案時只需更新這兩處。

## 已知限制

- 沒有登入：任何人都可以選擇他人的名字。
- 規則大致為全開放（只檢查餐廳及星數格式），保護方式只有「不公開網址」。
- 本機儲存的名字可能被 Safari 清除，需重新選擇。
- 食評及餐廳增加後，每次開啟的讀取量會上升，注意免費額度。
- 未有 PWA manifest 及推送提醒（排程提醒需要 Firebase Blaze 付費方案）。

## 安全提醒

- `firebaseConfig`（含 `apiKey`）屬公開識別資料，不是密碼；真正的保護來自 Firestore 規則。
- 切勿把 service account key、`.env` 或 Firebase CLI 登入憑證提交到 GitHub。
- 只把網址分享給信任的朋友。

## 給 AI 助手

開始工作前請先閱讀 `AGENTS.md` 及 `PROJECT.md`，完成後更新 `PROJECT.md` 的 Changelog。
