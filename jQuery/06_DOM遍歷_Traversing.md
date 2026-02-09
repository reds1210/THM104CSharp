# 第 6 章：DOM 遍歷 (Traversing)

## 本章目標
不依賴過多的 ID 或 Class，而是根據元素之間的相對關係來查找目標。

## 教學大綱

### 6.1 樹狀結構查找
- [ ] 向上找 (祖先)：`parent()` (上一層), `closest('selector')` (往上找最近的符合者 - **超實用**)
- [ ] 向下找 (後代)：`children()` (子層), `find('selector')` (所有後代 - **超實用**)
- [ ] 同層找 (兄弟)：`siblings()`, `next()`, `prev()`

### 6.2 條件判斷 【新增重點】
- [ ] `.is('selector')`：回傳 true/false
- [ ] 應用：在 click 事件中判斷 `if ($(this).is('.active'))`

### 6.3 過濾結果
- [ ] `.filter('selector')`：從選取集合中保留符合者
- [ ] `.not('selector')`：從選取集合中移除符合者
- [ ] `.eq(index)`：選取第 n 個

### 6.4 實作練習
- [ ] 練習 6：製作一個多層級的檔案總管介面。
    1. 點擊資料夾 icon 時，只展開該資料夾底下的檔案列表 (`$(this).closest(...).find(...)`)。
    2. 確保不會影響到其他層級的資料夾。
