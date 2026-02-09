# 第 1 章：環境建置與基礎觀念 (Environment & Basics)

## 本章目標
- 深入了解 jQuery 的歷史地位與現代價值。
- 理解為什麼現代新專案可能不再首選 jQuery，但我們為什麼還需要學習它。
- 掌握 `$(document).ready()` 的底層原理與 `window.onload` 的差異。
- 寫出你的第一個 jQuery 程式。

---

## 1.1 什麼是 jQuery？

jQuery 是一個快速、輕量級的 JavaScript 函式庫。它的核心哲學是 **"Write less, do more" (寫得更少，做得更多)**。

### 歷史背景：瀏覽器大戰時代的救世主
在 2006 年 jQuery 誕生之前，前端開發是一場惡夢。當時正值瀏覽器大戰（IE vs Firefox vs Netscape），不同瀏覽器對 JavaScript 的實作標準不一。
- 想要選取一個元素？IE 可能用 `document.all`，Firefox 用標準 DOM API。
- 想要發送 AJAX？你需要寫一大段 `if...else` 來判斷是使用 `XMLHttpRequest` 還是 `ActiveXObject`。
- 事件監聽？`addEventListener` vs `attachEvent`。

開發者花費 50% 的時間在處理「讓程式碼在 IE6 上也能跑」，而不是在開發功能。**jQuery 的出現統一了這一切**。它提供了一套標準的 API，幫你在底層處理掉所有瀏覽器的相容性爛攤子。

### 現代觀點：為什麼現在「不用」了？
隨著時間推移，Web 發生了兩大變化，導致 jQuery 的重要性下降：
1.  **瀏覽器標準化 (ES6+)**：現代瀏覽器 (Chrome, Edge, Safari) 非常遵循標準。原生 JavaScript 已經有了像 `document.querySelectorAll('.class')` 這樣好用的語法，也可以用 `fetch()` 輕鬆做 AJAX，不再那麼需要 jQuery 來抹平差異。
2.  **前端框架興起 (Vue, React, Angular)**：這是最大的殺手。
    - **jQuery 思維**：直接操作 DOM (Imperative)。例如：「找到那個按鈕 -> 改變它的顏色 -> 找到那個輸入框 -> 拿值」。
    - **現代框架思維**：數據驅動 (Declarative)。例如：「我有一個變數 `color`，按鈕的顏色綁定這個變數。當我改變數時，框架自動幫我更新畫面」。
    - 在大型應用中，直接操作 DOM 會導致程式碼變成「義大利麵條 (Spaghetti Code)」，難以維護。

### 那為什麼我們還要學 jQuery？
儘管新專案少用，但在 2024 年之後的今天，jQuery 依然是**必備技能**：
1.  **維護舊專案 (Legacy Code)**：世界上仍有超過 70% 的網站（如 WordPress 網站）依賴 jQuery。你工作時極有可能接手維護這些系統。
2.  **Bootstrap 依賴**：許多 UI 框架（如 Bootstrap 4 及部分 5 的外掛）仍依賴 jQuery。
3.  **快速原型開發**：如果你只是要做一個簡單的活動頁面、形象官網，不需要 React 那種龐大的 Build Process，引入一個 jQuery 檔案直接寫，開發速度依然是最快的。

---

## 1.2 為什麼要選用 jQuery？ (The Benefits)

在我們開始寫程式之前，先總結一下使用 jQuery 的具體好處：

### 1. 跨瀏覽器兼容 (Cross-Browser Compatibility)
這是 jQuery 最核心的價值。你不需要擔心使用者的瀏覽器是 Chrome、Edge、Safari 還是舊版 IE。jQuery 團隊已經幫你測試過所有邊角案例 (Edge Cases)，確保你的程式碼在任何地方都能正常運作。

### 2. 鏈式語法 (Chaining)
jQuery 允許你在同一行程式碼中對同一個元素執行多個動作，這讓程式碼非常簡潔易讀。
```javascript
$("#p1").css("color", "red").slideUp(2000).slideDown(2000);
```
這行程式碼做了三件事：變紅色 -> 滑動收起 -> 滑動展開。原生 JS 要寫這三步會繁瑣得多。

### 3. 隱式迭代 (Implicit Iteration)
在原生 JS 中，如果你想隱藏所有的 `<p>` 元素，你需要寫一個迴圈：
```javascript
var ps = document.querySelectorAll("p");
for (var i = 0; i < ps.length; i++) {
    ps[i].style.display = "none";
}
```
但在 jQuery 中，你完全不需要寫迴圈！jQuery 會自動幫你遍歷所有選到的元素：
```javascript
$("p").hide(); // 所有 <p> 都會被隱藏
```

### 4. 豐富的插件生態 (Plugins)
由於 jQuery 曾經統治前端十幾年，網路上有成千上萬個現成的插件。無論你需要輪播圖 (Carousel)、燈箱 (Lightbox)、日期選擇器 (Datepicker) 還是表單驗證，幾乎都能找到成熟穩定的 jQuery 插件直接套用。

### 5. 極簡的 AJAX
雖然現代有 `fetch()`，但 jQuery 的 `$.ajax()` 依然是處理複雜請求（如 JSONP、Timeout 設定、錯誤處理）的強大工具，且語法直觀。

---

## 1.3 環境建置 (Setup)

要使用 jQuery，你不需要安裝任何軟體，只需要在 HTML 檔案中引入它的 JavaScript 檔案即可。

### 使用 CDN 引入 (推薦)
將以下 `<script>` 標籤貼在你的 HTML 檔案的 `<head>` 標籤內，或是 `<body>` 結束標籤 `</body>` 之前。

**jQuery 3.x (現代版本):**
```html
<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
```

---

## 1.4 核心語法

jQuery 的語法設計是為了「選取 HTML 元素」並對其「執行操作」。

### 基本公式
```javascript
$(selector).action()
```
或者寫成全名（這在避免衝突時很有用）：
```javascript
jQuery(selector).action()
```

- **`$` 符號**: 這其實只是一個變數名稱！jQuery 在載入時，把 `window.jQuery` 這個函式賦值給了 `window.$`。所以 `$` **就是** `jQuery`。
- **`(selector)`**: 選擇器字串。jQuery 會根據這個字串去 DOM 樹中尋找對應的元素，並把它們包裝成一個 **jQuery 物件 (jQuery Object)** 回傳。注意，這**不是**原生的 DOM 元素，所以你不能對它直接用原生 JS 的屬性（如 `.style` 或 `.value`），必須用 jQuery 提供的方法。
- **`.action()`**: jQuery 方法。例如 `.hide()`, `.css()`, `.click()`。

### 語法對比
假設我們要將 id 為 "demo" 的元素文字改成 "Hello"：

**原生 JS:**
```javascript
var el = document.getElementById("demo"); 
if (el) { // 原生要小心如果是 null 會報錯
    el.innerText = "Hello";
}
```

**jQuery:**
```javascript
$("#demo").text("Hello"); // 如果找不到 #demo，jQuery 什麼都不會做，不會報錯（容錯性高）
```

**jQuery 全名寫法 (效果一樣):**
```javascript
jQuery("#demo").text("Hello");
```

---

## 1.5 文檔就緒事件 (Document Ready) 的原理

這是初學者最容易遇到的坑！
如果你的 JavaScript 程式碼在 HTML 元素還沒載入完成之前就執行，程式會找不到該元素。

### 為什麼需要它？
瀏覽器讀取 HTML 是**由上而下**解析的。
```html
<head>
  <script> $("#btn").click(...); // 錯誤！此時瀏覽器還沒讀到下方的 <body>，#btn 根本不存在 </script>
</head>
<body>
  <button id="btn">Click me</button>
</body>
```
為了讓 `<head>` 裡的程式碼能操作 `<body>` 裡的元素，我們必須等待瀏覽器發出「我讀完整個 HTML 架構了」的訊號。

### `$(document).ready()` vs `window.onload`
在原生 JS 中，我們常聽到 `window.onload`，這兩者有顯著的效能差異：

1.  **`window.onload`**: 必須等到網頁上**所有資源**（包含圖片、廣告 banner、外部 CSS）都完全下載完畢才會執行。如果圖片很大，使用者可能等了 10 秒都無法點擊按鈕。
2.  **`$(document).ready()`**: 只要 **DOM 結構 (HTML 標籤)** 繪製完成就執行，**不用等圖片**下載。這通常只需幾毫秒，使用者體驗好非常多。

### 標準寫法
```javascript
$(document).ready(function(){
   // 這裡的程式碼會在 DOM 結構繪製完後立刻執行
   console.log("Ready!");
});
```

### 簡寫寫法 (實戰標準)
jQuery 允許你直接把 function 傳給 `$()`，它會自動被視為 ready event。這是業界最通用的寫法：

```javascript
$(function(){
   // 這裡放你的初始化程式碼
   console.log("DOM is ready!");
});
```

---

## 1.6 完整範例

讓我們把所有東西組合起來，看看一個完整的 HTML 檔案長什麼樣子。

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <title>My First jQuery</title>
    <!-- 1. 引入 jQuery -->
    <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
    
    <script>
        // 2. 確保 DOM 結構繪製完成 (使用簡寫)
        $(function(){
            // 3. 測試 jQuery 是否正常運作
            alert("jQuery is working!");
        });
    </script>
</head>
<body>

    <h1>Hello jQuery!</h1>

</body>
</html>
```

---

## 1.7 課後練習 (Exercise)

請你動手建立一個 HTML 檔案，完成以下任務：

**任務目標：**
1. 建立一個 `index.html`。
2. 使用 CDN 引入 jQuery。
3. 使用 `$(function(){ ... })` 確保 DOM 載入完成。
4. 在函式內部，使用 `console.log("jQuery 環境建置成功！")` 輸出訊息。
5. 開啟瀏覽器的開發者工具 (F12) -> Console 面板，確認是否有看到這則訊息。

> **提示：** 這是確保你開發環境正確的第一步。如果 Console 出現 `Uncaught ReferenceError: $ is not defined`，代表你沒有成功引入 jQuery 檔案。

---

### 練習解答 (Solution)

<details>
<summary>點擊展開查看解答程式碼</summary>

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQuery 練習 1 解答</title>
    <!-- 步驟 2: 引入 jQuery CDN -->
    <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
    
    <script>
        // 步驟 3: Document Ready 事件
        $(function() {
            // 步驟 4: 輸出訊息到控制台
            console.log("jQuery 環境建置成功！");
        });
    </script>
</head>
<body>
    <h1>請打開 F12 Console 查看結果</h1>
</body>
</html>
```
</details>
