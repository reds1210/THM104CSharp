# 06. 敏捷技術實踐 (Technical Practices)

敏捷不只是開會，它需要強大的技術支撐。如果沒有這些實踐，敏捷會變成「快速地製造垃圾」。

## 1. 持續整合 (Continuous Integration, CI)
**解決痛點**: 避免「整合地獄」(Integration Hell)。

*   **作法**: 
    1. 頻繁 Commit：每人每天至少推程式碼到 GitHub 一次。
    2. 自動化測試：推上去後，自動跑單元測試。
    3. 自動建置：確保程式碼沒寫壞，大家都能編譯成功。
*   **專題工具**: GitHub Actions (推薦，學生免費且易上手)。

## 2. 測試驅動開發 (TDD) 與 自動化測試
**解決痛點**: 「改 A 壞 B」，不敢動別人的程式碼。

*   **觀念**: 紅燈 (寫測試失敗) -> 綠燈 (寫程式通過) -> 重構 (美化程式碼)。
*   **對專題的建議**: 如果沒時間全寫測試，至少針對「核心邏輯」(例如：結帳計算金額、權限判斷) 撰寫單元測試。

## 3. 程式碼審查 (Code Review)
**解決痛點**: 只有寫的人懂那段 Code，他請假全組就癱瘓。

*   **作法**: 
    *   在 GitHub 使用 **Pull Request (PR)**。
    *   規定至少要有另外一位組員看過並 Approve，才能 Merge 到 `main` 分支。
*   **目的**: 知識共享、發現潛在 Bug、統一命名規則。

## 4. 結對編程 (Pair Programming)
**解決痛點**: 新手跟不上，強者很孤單。

*   **作法**: 兩個人共用一台電腦 (或線上 VS Code Live Share)。
    *   **駕駛 (Driver)**: 寫程式。
    *   **導航員 (Navigator)**: 思考邏輯、檢查錯誤、規劃下一步。
*   **專題效益**: 這是最強的技術轉移方式，新手進步最快。

---

## 5. 專題實作練習 (Homework)

### 任務：打造自動化守門員
1.  **GitHub 設定**: 在你們的 Repository 設定 `Branch protection rule`。
    *   勾選 `Require a pull request before merging`。
    *   勾選 `Require status checks to pass before merging` (如果有設 CI)。
2.  **試行 PR**: 今天的任何小修改，都不准直接 Push 到 `main`，必須開分支 (Branch) 並請隊友 PR Review。
