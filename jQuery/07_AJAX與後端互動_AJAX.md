# 第 7 章：AJAX 與後端互動 (AJAX & Backend)

## 本章目標
學會現代化的非同步資料交換技術，實現不刷新頁面的資料更新。

## 教學大綱

### 7.1 AJAX 概念
- [ ] 什麼是 AJAX？
- [ ] 同步 (Synchronous) vs 非同步 (Asynchronous) 的體驗差異

### 7.2 簡易請求方法
- [ ] `$('#div').load('url')`：最簡單的 HTML 載入
- [ ] `$.get(url, data)`
- [ ] `$.post(url, data)`

### 7.3 核心 `$.ajax()` 與 Promise 介面 【新增重點】
- [ ] 基本設定：`url`, `method`, `data`, `dataType: 'json'`
- [ ] **舊式寫法** (不推薦)：`success`, `error`
- [ ] **現代 Promise 寫法 (推薦)**：
    - `.done(function(response) { ... })`
    - `.fail(function(xhr) { ... })`
    - `.always(function() { ... })` (常用於關閉 Loading spinner)

### 7.4 實戰技巧
- [ ] 表單序列化：`$('form').serialize()` (快速打包表單資料送後端)
- [ ] 處理 JSON 資料：迴圈渲染列表
- [ ] 錯誤除錯：查看 Network Tab 與 Console Log

### 7.5 實作練習
- [ ] 練習 7：串接公開 API (如 Random User Generator)。
    1. 點擊按鈕發送 AJAX 請求。
    2. 顯示 "載入中..." 動畫。
    3. 成功 (`done`)：渲染使用者大頭貼與姓名。
    4. 失敗 (`fail`)：顯示錯誤訊息。
    5. 結束 (`always`)：隱藏 "載入中..."。
