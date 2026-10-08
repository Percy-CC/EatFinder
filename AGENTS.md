# AGENTS.md

此專案名稱：食乜好（午餐意願及餐廳食評 Web App）。

## 開始工作前（必做）
1. 先完整閱讀根目錄的 `PROJECT.md`，特別是「Decisions」「Known Limitations」「Changelog」。
2. 不要憑記憶或猜測更改架構。若 `PROJECT.md` 與代碼不一致，以代碼為準，並在 `PROJECT.md` 更正。
3. 若用戶要求與 `PROJECT.md` 的 Decisions 衝突，先向用戶指出衝突再動手。

## 工作期間
- 沒有 build step：整個前端只有 `index.html`，直接用 Firebase JS SDK CDN。除非用戶同意，不要引入 npm、打包工具或框架。
- 用戶介面語言：香港粵語口語（繁體中文）。新增文字要保持相同語氣。
- 回覆用戶時使用繁體中文；若問題以英文提出，保留英文關鍵字。
- 不要把 `firebaseConfig` 的真實值、service account key、`firebase login` 憑證寫入文件或提交。
- 修改 `firestore.rules` 後，提醒用戶執行：`npx firebase-tools deploy --only firestore:rules`。
- 所有改動以最小範圍進行，不要重寫整個 `index.html`，除非用戶要求。

## 完成工作後（必做）
1. 在 `PROJECT.md` 的 Changelog 最上方新增一條記錄（日期、改動檔案、原因、影響）。
2. 若改動了資料結構、規則、功能或決定，同步更新對應章節（Data Model、Security Rules、Features、Decisions）。
3. 若發現新限制或未完成事項，加入 Known Limitations 或 TODO。
4. 在回覆結尾列出：改了什麼、哪些檔案、用戶是否需要重新部署（Git push 或 rules deploy）。

## 誠實原則
- 沒有實際測試過的代碼，要明確說「未測試」。
- 不確定的事要標明，不要編造 Firebase 或 GitHub 的設定步驟。