# 第 8 章：工具方法與進階技巧 (Utilities & Advanced)

## 本章目標
掌握 jQuery 提供的強大工具函式，以及如何擴充 jQuery 功能。

## 教學大綱

### 8.1 陣列與物件處理
- [ ] 通用迴圈：`$.each(collection, callback)`
    - 區別 `$(selector).each()`
- [ ] **陣列轉換**：`$.map(array, callback)` 【新增重點】
    - 用途：將 `[{id:1, name:'A'}, {id:2, name:'B'}]` 轉換為 `[1, 2]`
- [ ] 物件合併：`$.extend(target, object1, object2)` (常用於設定預設參數)
- [ ] 搜尋陣列：`$.inArray(value, array)`

### 8.2 進階議題
- [ ] 衝突處理：`$.noConflict()`
- [ ] 鏈式語法 (Chaining) 的原理
- [ ] 效能優化建議 (暫存 jQuery 物件、減少 DOM 操作)

### 8.3 擴充 jQuery (Plugin)
- [ ] 簡單插件寫法：`$.fn.myFunction = function() { ... }`
- [ ] 實作一個簡單的 "Highlight" 插件

### 8.4 課程總結
- [ ] jQuery 的現狀與未來
- [ ] 與 Vue/React/Angular 的共存之道

### 8.5 實作練習
- [ ] 練習 8：撰寫一個簡易的 jQuery Plugin `$.fn.changeColor`，讓使用者可以呼叫 `$('div').changeColor('red')` 來改變背景色。
