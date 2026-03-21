# 第 7 章：AJAX 與後端互動 (AJAX & Backend)

## 本章目標
- 理解 AJAX 的核心概念：實現不刷新頁面的動態資料更新。
- 掌握同步 (Sync) 與非同步 (Async) 的體驗差異。
- 學會使用簡易方法 (`load`, `get`, `post`, `getJSON`)。
- **精通核心 `$.ajax()` 設定與現代 Promise 介面**。
- **掌握全域 AJAX 事件與安全性設定 (Headers/CSRF)**。
- 實作自動化表單序列化與外部 API 串接。

---

## 7.1 AJAX 基礎概念 (Core Concepts)

### 什麼是 AJAX？
AJAX 全名為 **A**synchronous **J**avaScript **A**nd **X**ML。它允許網頁在背景與伺服器交換資料。

### 同步 vs 非同步 (Sync vs Async)
- **同步 (Synchronous)**：像打電話，對方不掛斷你就不能做別的事（頁面卡死）。
- **非同步 (Asynchronous)**：像傳簡訊，發出去後你可以繼續逛街，等對方回覆了再看（頁面流暢）。

---

## 7.2 簡易請求方法 (Shorthand Methods)

### 1. `load()` - 載入局部 HTML
將遠端的 HTML 片段直接填入目前的元素。

**範例操作：**
```javascript
// 點擊時載入公告內容
$("#btn").click(function() {
    $("#content").load("news_fragment.html");
});
```

### 2. `$.get()` / `$.post()` / `$.getJSON()`
- **`$.get(url, data, callback)`**: 索取資料。
- **`$.post(url, data, callback)`**: 傳送資料。
- **`$.getJSON(url, data, callback)`**: 專門獲取 JSON 格式，會自動轉成 JS 物件。

**範例操作：**
```javascript
// 快速獲取天氣 JSON
$.getJSON("https://api.weather.com/today", function(data) {
    console.log("今日溫度：" + data.temp);
});
```

---

## 7.3 核心 `$.ajax()` 深度設定

當你需要處理複雜邏輯（如設定 Header、處理錯誤、傳送 JSON 字串）時，必須使用 `$.ajax()`。

### 1. 關鍵參數說明
- **`url`**: 請求的伺服器位址。
- **`method`**: 請求方式 (GET, POST, PUT, DELETE)。
- **`data`**: 送往伺服器的資料（物件或字串）。
- **`dataType`**: **預期從伺服器回傳**的資料格式 (json, html, text)。
- **`contentType`**: **送往伺服器時**的編碼格式。預設為表單格式 `application/x-www-form-urlencoded`。

### 2. 現代 Promise 寫法 (推薦)
**範例操作：**
```javascript
$.ajax({
    url: "/api/products",
    method: "POST",
    data: JSON.stringify({ id: 101, name: "耳機" }), // 轉為 JSON 字串
    contentType: "application/json; charset=utf-8", // 告訴後端我傳的是 JSON
    dataType: "json"
})
.done(function(res) { console.log("成功", res); })
.fail(function(xhr) { console.log("失敗", xhr.status); })
.always(function() { console.log("結束"); });
```

---

## 7.4 安全性與全域事件 (Advanced AJAX)

### 1. 設定 Header (處理 CSRF Token)
在 ASP.NET Core MVC 中，為了防範 CSRF 攻擊，POST 請求通常需要攜帶 Token。

**範例操作：**
```javascript
$.ajax({
    url: "/Member/Update",
    method: "POST",
    headers: {
        "RequestVerificationToken": $('input[name="__RequestVerificationToken"]').val()
    },
    data: { name: "新名字" }
});
```

### 2. 全域 AJAX 事件 (Global Events)
如果你希望網頁上「任何」一個 AJAX 執行時都顯示 Loading，不需要在每個 `$.ajax` 裡寫 `show/hide`，直接綁定在 `document` 上即可。

**範例操作：**
```javascript
// 只要有任何 AJAX 開始，就顯示 Loading 轉圈
$(document).ajaxStart(function() {
    $("#global-spinner").show();
});

// 所有的 AJAX 都結束後，隱藏 Loading
$(document).ajaxStop(function() {
    $("#global-spinner").hide();
});
```

---

## 7.5 實戰技巧：表單處理 (Form Handling)

### 1. `serialize()` vs `FormData`
- **`serialize()`**: 將表單轉為 `name=value&name2=value2` 字串。適合一般純文字表單。
- **`FormData`**: HTML5 原生物件，適合包含「檔案上傳」的表單。

**範例操作：**
```javascript
$("#myForm").on("submit", function(e) {
    e.preventDefault();
    
    $.ajax({
        url: "/api/save",
        method: "POST",
        data: $(this).serialize() // 自動打包所有具備 name 的欄位
    }).done(function() { alert("儲存成功"); });
});
```

---

## 7.6 AJAX 方法對照表 (Cheat Sheet)

| 方法 | 用途 | 適用場景 |
| :--- | :--- | :--- |
| **`load()`** | 載入 HTML 片段 | 側邊欄、分頁內容 |
| **`$.get()`** | 簡單 GET 請求 | 搜尋建議、獲取設定值 |
| **`$.post()`** | 簡單 POST 請求 | 提交評論、登入 |
| **`$.ajax()`** | 完整控制 | 檔案上傳、需要 Header、處理 JSON 字串 |
| **`$.getScript()`** | 動態載入 JS | 延遲載入地圖、圖表庫 |

---

### [隨堂小測驗]
**任務：**
1. 在 `$.ajax` 設定中，如果你希望伺服器知道你傳送的是 JSON 字串，應該設定哪個屬性？
   ```javascript
   ____: "application/json"
   ```
2. 如何讓全網頁的 AJAX 請求在出錯時統一跳出 alert？
   ```javascript
   $(document).____(function(event, xhr, settings, error) { alert("出錯了！"); });
   ```

<details>
<summary>查看解答</summary>

1. **contentType**
2. **ajaxError** (這也是全域事件之一)
</details>

---

## 7.7 綜合課後練習 (Exercise)

**專案：會員資料快速編輯器 (Quick Editor)**

**任務目標：**
1. 建立一個 `index.html`：
```html
<div id="loading" style="display:none; position:fixed; top:10px; right:10px; background:red; color:white;">載入中...</div>

<form id="profileForm">
    姓名：<input type="text" name="Username" value="張三"><br>
    信箱：<input type="email" name="Email" value="zhang@test.com"><br>
    <button type="submit">更新資料</button>
</form>

<div id="result"></div>
```

2. 撰寫 jQuery 程式碼完成以下功能：
   - **功能 A：全域 Loading**。使用 `ajaxStart` 與 `ajaxStop` 控制 `#loading` 的顯示。
   - **功能 B：串接真實 API**。使用 `https://jsonplaceholder.typicode.com/posts` (測試用 API) 進行 POST 請求。
   - **功能 C：表單序列化**。使用 `serialize()` 打包資料。
   - **功能 D：回饋顯示**。成功後在 `#result` 顯示「更新成功：」加上回傳的資料。
   - **功能 E：(挑戰)**。在發送請求前，手動在 Header 加入一個 `Authorization: Bearer test12345`。

---

### 練習解答 (Solution)

<details>
<summary>查看解答程式碼</summary>

```javascript
$(function() {
    // 功能 A: 全域事件
    $(document).ajaxStart(function() { $("#loading").fadeIn(); });
    $(document).ajaxStop(function() { $("#loading").fadeOut(); });

    // 功能 B, C, D, E
    $("#profileForm").submit(function(e) {
        e.preventDefault();

        $.ajax({
            url: "https://jsonplaceholder.typicode.com/posts",
            method: "POST",
            headers: {
                "Authorization": "Bearer test12345"
            },
            data: $(this).serialize()
        })
        .done(function(res) {
            $("#result").html("<p style='color:green;'>更新成功！伺服器回傳 ID: " + res.id + "</p>");
        })
        .fail(function() {
            alert("請求失敗");
        });
    });
});
```
</details>
