# 第 2 章：選擇器 (Selectors)

## 本章目標
- 掌握 jQuery 最強大的功能：精準選取 DOM 元素。
- 學會使用 ID、Class、元素選擇器。
- 深入了解**屬性選擇器 (Attribute Selectors)** 的強大應用。
- 熟悉層次選擇器與表單專用選擇器。

---

## 2.1 為什麼選擇器這麼重要？

在 jQuery 中，幾乎所有的操作都遵循這個模式：
`$( "選取目標" ).action()`

如果你選不到它，你就沒辦法操作它。jQuery 的選擇器引擎 (Sizzle Engine) 非常強大，它允許你使用 CSS 語法來查找元素，甚至支援一些 CSS 做不到的複雜篩選。

---

## 2.2 基礎選擇器 (Basic Selectors)

這是最常用、效能也最好的三種選擇器。

### 1. ID 選擇器 (`#id`)
選取具有特定 ID 的**唯一**元素。
```javascript
$("#header").hide(); // 隱藏 id="header" 的元素
```

### 2. 類別選擇器 (`.class`)
選取所有具有特定 Class 的元素。
```javascript
$(".error").css("color", "red"); // 將所有 class="error" 的文字變紅
```

### 3. 元素選擇器 (`element`)
選取所有特定 HTML 標籤。
```javascript
$("p").css("font-size", "18px"); // 設定所有 <p> 的字體大小
```

### 4. 多重選取 (Multiple Selector)
一次選取多個不同的元素，用逗號分隔。
```javascript
$("h1, h2, #banner").addClass("highlight");
```

### [隨堂小測驗 1]
**準備工作：** 請複製以下 HTML 到你的檔案中。
```html
<h1 class="title">大標題</h1>
<p class="intro">這是介紹文字。</p>
<div id="content">主要內容區域</div>
<p class="footer">頁尾</p>
```

**任務：** 請填寫 `$(...)` 裡面的選擇器，讓程式碼生效。

1. 選取 ID 為 `content` 的元素：
   ```javascript
   $(______).css("background", "yellow");
   ```
2. 同時選取 `h1` 和 `.intro`：
   ```javascript
   $(______).css("color", "blue");
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. ID 選擇器
$("#content").css("background", "yellow");

// 2. 多重選擇器 (元素 + Class)
$("h1, .intro").css("color", "blue");
```
</details>

---

## 2.3 層次選擇器 (Hierarchy Selectors)

當元素沒有 ID 或 Class 時，我們通常會根據它們的「親戚關係」來尋找。

### 1. 後代選擇器 (Descendant) `空格`
選取某元素內部的**所有**特定後代（不管隔幾層）。

**範例情境：**
```html
<ul id="menu">
    <li>Item 1</li>      <!-- 選中 -->
    <li>                 <!-- 選中 -->
        <ul>
            <li>Sub 1</li> <!-- 也選中！因為它是後代 -->
        </ul>
    </li>
</ul>
```
```javascript
// 選取 <ul id="menu"> 裡面所有的 <li>
$("#menu li").css("border", "1px solid blue");
```

### 2. 子元素選擇器 (Child) `>`
只選取**直接**子元素（只向下找一層）。

**範例情境：** (同上 HTML)
```javascript
// 只選取 <ul id="menu"> 第一層的 <li>
// "Sub 1" 不會被選中，因為它是孫子層
$("#menu > li").css("border", "1px solid red");
```

### 3. 緊鄰兄弟 (Next Adjacent) `+`
選取緊接在某元素**後方**且**同層**的第一個元素。

**範例情境：**
```html
<h2>標題</h2>
<p>段落 1</p> <!-- 選中！因為它緊跟在 h2 後面 -->
<p>段落 2</p> <!-- 不選中 -->
```
```javascript
// 選取緊接在 h2 後面的 p
$("h2 + p").css("background", "yellow");
```

### 4. 後續兄弟 (Next Siblings) `~`
選取某元素之後的**所有**同層兄弟元素。

**範例情境：**
```html
<div id="start">起點</div>
<span>我是 span</span> <!-- 不選中 (不是 p) -->
<p>段落 A</p>          <!-- 選中！在 #start 後面且是 p -->
<p>段落 B</p>          <!-- 選中！在 #start 後面且是 p -->
```
```javascript
// 選取 #start 之後的所有 p
$("#start ~ p").css("color", "orange");
```

### [隨堂小測驗 2]
**準備工作：** 請複製以下 HTML。
```html
<div id="container">
    <p>我是第一層的段落 (兒子)</p>
    <div class="inner">
        <p>我是第二層的段落 (孫子)</p>
    </div>
    <h3 id="mark">小標題</h3>
    <p>標題後的段落 1</p>
    <p>標題後的段落 2</p>
</div>
```

**任務：** 請填寫選擇器。

1. 只選取 `#container` 的「兒子」段落 (字體變大)：
   ```javascript
   $(______).css("font-size", "24px");
   ```
2. 選取緊接在 `#mark` 後面的**那一個**段落 (背景變黃)：
   ```javascript
   $(______).css("background", "yellow");
   ```
3. 選取 `#mark` 後面**所有的**同層段落 (文字變紅)：
   ```javascript
   $(______).css("color", "red");
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. 子元素選擇器
$("#container > p").css("font-size", "24px");

// 2. 緊鄰兄弟選擇器
$("#mark + p").css("background", "yellow");

// 3. 後續兄弟選擇器
$("#mark ~ p").css("color", "red");
```
</details>

---

## 2.4 屬性選擇器 (Attribute Selectors) 【實戰重點】

這是處理表單、連結或特定資料屬性時的神器。

| 語法 | 描述 | 範例 |
| :--- | :--- | :--- |
| `[href]` | 選取有 href 屬性的元素 | `$("[href]")` |
| `[attr="value"]` | 屬性值**完全等於** | `$("input[type='text']")` (選取文字框) |
| `[attr^="value"]` | 屬性值以...**開頭** | `$("a[href^='https']")` (選取安全連結) |
| `[attr$="value"]` | 屬性值以...**結尾** | `$("img[src$='.png']")` (選取 PNG 圖片) |
| `[attr*="value"]` | 屬性值**包含** | `$("input[name*='user']")` (選取 name 包含 user 的欄位) |

**實戰範例：**
```javascript
// 將所有連到外部網站 (http 開頭) 的連結設為在新分頁開啟
// .attr() 是修改屬性的方法，下一章會教
$("a[href^='http']").attr("target", "_blank");
```

### [隨堂小測驗 3]
**準備工作：** 請複製以下 HTML。
```html
<ul>
    <li><a href="http://google.com">Google (http)</a></li>
    <li><a href="https://secure.com">Secure Bank (https)</a></li>
    <li><a href="/about.html">關於我們 (本地)</a></li>
    <li><a href="http://yahoo.com">Yahoo (http)</a></li>
</ul>
```

**任務：** 請填寫選擇器。

1. 只選取「以 `https` 開頭」的連結 (修改文字為 Secured Link)：
   ```javascript
   $(______).text("Secured Link");
   ```
2. 選取所有「以 `http`」開頭的連結 (文字變紅)：
   ```javascript
   $(______).css("color", "red");
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. 開頭匹配 https
$("a[href^='https']").text("Secured Link");

// 2. 開頭匹配 http
$("a[href^='http']").css("color", "red");
```
</details>

---

## 2.5 基本篩選器 (Basic Filters)

這些篩選器通常不會單獨使用，而是**「接在」其他選擇器後面**，用來進一步過濾結果。

### 1. `:first` 與 `:last`
選取集合中的第一個或最後一個。

**範例情境：**
```html
<ul id="menu">
    <li>首頁 (First)</li>
    <li>關於我們</li>
    <li>產品介紹</li>
    <li>聯絡我們 (Last)</li>
</ul>
```
```javascript
// 限制在 #menu 範圍內，只選取第一個 li
$("#menu li:first").css("border", "2px solid red");
```

### 2. `:even` 與 `:odd` (常用於斑馬紋表格)
*   **注意：** 索引 (Index) 是從 **0** 開始計算。
    *   `:even` (偶數)：0, 2, 4... (視覺上的第 1, 3, 5 行)
    *   `:odd` (奇數)：1, 3, 5... (視覺上的第 2, 4, 6 行)

**範例情境：**
```html
<table class="data-table">
    <tr><td>Row 1 (Index 0 - Even)</td></tr>
    <tr><td>Row 2 (Index 1 - Odd)</td></tr>
    <tr><td>Row 3 (Index 2 - Even)</td></tr>
</table>
```
```javascript
// 選取 .data-table 裡面的所有偶數列 (tr)，將背景變灰
$(".data-table tr:even").css("background", "#eee");
```

### 3. `:eq(index)` (指定索引)
選取索引等於 n 的那一個元素。

**範例情境：**
```html
<ul class="nav">
    <li>Home (0)</li>
    <li>Profile (1)</li>
    <li>Settings (2)</li> <!-- 目標 -->
    <li>Logout (3)</li>
</ul>
```
```javascript
// 選取 .nav 列表中的第 3 個 li (索引為 2)
$(".nav li:eq(2)").addClass("active");
```

### 4. `:gt(n)` 與 `:lt(n)` (大於與小於)
選取索引大於或小於 n 的所有元素。

**範例情境：**
```html
<ul class="ranking">
    <li>第 1 名 (0)</li>
    <li>第 2 名 (1)</li>
    <li>第 3 名 (2)</li>
    <li>第 4 名 (3)</li> <!-- gt(2) 從這裡開始選 -->
    <li>第 5 名 (4)</li>
</ul>
```
```javascript
// 隱藏列表中的第 4 名以後的所有項目 (索引 > 2)
$(".ranking li:gt(2)").hide();
```

### [隨堂小測驗 4]
**準備工作：** 請複製以下 HTML。
```html
<ul id="news-list">
    <li>頭條新聞 (Index 0)</li>
    <li>新聞 A (Index 1)</li>
    <li>新聞 B (Index 2)</li>
    <li>新聞 C (Index 3)</li>
    <li>新聞 D (Index 4)</li>
</ul>
```

**任務：**
1. 選取 `#news-list` 裡面的**第一個** `li` (文字變紅)：
   ```javascript
   $(______).css("color", "red");
   ```
2. 選取 `#news-list` 裡面索引**大於 2** (即新聞 C, D) 的所有 `li` (背景變灰)：
   ```javascript
   $(______).css("background", "#ccc");
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. 搭配 ID 與 :first
$("#news-list li:first").css("color", "red");

// 2. 搭配 ID 與 :gt
$("#news-list li:gt(2)").css("background", "#ccc");
```
</details>

---

## 2.6 表單專用選擇器 (Form Selectors)

專門用來選取表單元素及其狀態，通常搭配表單 ID 或 Input 名稱使用。

### 1. 類型選取
- `:input`：選取所有 `<input>`, `<textarea>`, `<select>`, `<button>`。
- `:text`：選取 `type="text"` 的 input。
- `:password`：選取密碼框。

**範例情境：**
```html
<form id="login-form">
    帳號：<input type="text"> <!-- 目標 -->
    密碼：<input type="password">
    <button>登入</button>
</form>
```
```javascript
// 選取 #login-form 裡面所有的文字輸入框
$("#login-form :text").css("background", "yellow");
```

### 2. 狀態選取 (實戰最高頻率)
- `:checked`：選取被勾選的 `checkbox` 或 `radio`。
- `:selected`：選取下拉選單 `<select>` 中被選中的 `<option>`。
- `:disabled`：選取被禁用的表單元素。

**範例情境 1：取得 Radio 值**
```html
<label><input type="radio" name="gender" value="male" checked> 男</label> <!-- 目標 -->
<label><input type="radio" name="gender" value="female"> 女</label>
```
```javascript
// 選取 name="gender" 且被勾選 (:checked) 的 input
var gender = $("input[name='gender']:checked").val(); // 結果："male"
```

**範例情境 2：操作下拉選單**
```html
<select id="city">
    <option value="TPE">台北</option>
    <option value="TXG" selected>台中</option> <!-- 目標 -->
    <option value="KHH">高雄</option>
</select>
```
```javascript
// 選取 ID 為 city 的 select 中被選中 (:selected) 的 option
var city = $("#city option:selected").text(); // 結果："台中"
```

**範例情境 3：禁用狀態**
```html
<button class="btn">可用按鈕</button>
<button class="btn" disabled>禁用按鈕</button> <!-- 目標 -->
```
```javascript
// 選取 class 為 btn 且是禁用狀態 (:disabled) 的按鈕
$(".btn:disabled").css("opacity", "0.5");
```

### [隨堂小測驗 5]
**準備工作：** 請複製以下 HTML。
```html
<form id="survey">
    <p>姓名：<input type="text" name="username"></p>
    <p>興趣：
       <input type="checkbox" name="hobby" value="coding" checked> Coding
       <input type="checkbox" name="hobby" value="reading"> Reading
    </p>
    <p><button class="submit-btn" disabled>送出</button></p>
</form>
```

**任務：**
1. 選取 `#survey` 表單內的所有 `text` 輸入框 (背景變黃)：
   ```javascript
   $(______).css("background", "yellow");
   ```
2. 選取 `name="hobby"` 且**被勾選**的 checkbox (隱藏它)：
   ```javascript
   $(______).hide();
   ```
3. 選取 class 為 `.submit-btn` 且**被禁用**的按鈕 (邊框變紅)：
   ```javascript
   $(______).css("border", "2px solid red");
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. ID 搭配 :text
$("#survey :text").css("background", "yellow");

// 2. 屬性選擇器搭配 :checked
$("input[name='hobby']:checked").hide();

// 3. Class 搭配 :disabled
$(".submit-btn:disabled").css("border", "2px solid red");
```
</details>

---

## 2.7 課後練習 (Exercise)

**情境：** 你正在製作一個商品列表頁面。

**任務目標：**
1. 建立一個 `index.html`，並複製貼上以下 HTML 結構：
```html
<h2>商品列表</h2>
<ul id="product-list">
    <li class="item" data-category="food">蘋果 (Food)</li>
    <li class="item promo" data-category="drink">可樂 (Drink, Promo)</li>
    <li class="item" data-category="food">麵包 (Food)</li>
    <li class="item out-of-stock" data-category="electronic">手機 (Out of Stock)</li>
    <li class="item" data-category="drink">水 (Drink)</li>
</ul>
```
2. 請複製以下 JavaScript 樣板，並填入正確的選擇器：
```javascript
$(function() {
    // 1. 將所有 "特價品" (.promo) 的文字顏色變為紅色
    $("______").css("color", "red");

    // 2. 將所有 "缺貨商品" (.out-of-stock) 的背景顏色變為灰色
    $("______").css("background-color", "#ddd");

    // 3. 使用 "屬性選擇器"，將所有 data-category 為 food 的商品加上粗體
    $("______").css("font-weight", "bold");

    // 4. 使用 "搭配語法"：選取 #product-list 裡面的偶數列 (斑馬紋)
    $("______").css("background-color", "#f9f9f9");
});
```

---

### 練習解答 (Solution)

<details>
<summary>點擊展開查看解答程式碼</summary>

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <title>jQuery 選擇器練習</title>
    <style>
        ul { list-style: none; padding: 0; }
        li { padding: 10px; border-bottom: 1px solid #ccc; }
    </style>
    <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
    <script>
        $(function() {
            $(".promo").css("color", "red");
            $(".out-of-stock").css("background-color", "#ddd");
            $("li[data-category='food']").css("font-weight", "bold");
            
            // 4. 搭配 ID 使用
            $("#product-list li:even").css("background-color", "#f9f9f9");
        });
    </script>
</head>
<body>
    <h2>商品列表</h2>
    <ul id="product-list">
        <li class="item" data-category="food">蘋果 (Food)</li>
        <li class="item promo" data-category="drink">可樂 (Drink, Promo)</li>
        <li class="item" data-category="food">麵包 (Food)</li>
        <li class="item out-of-stock" data-category="electronic">手機 (Out of Stock)</li>
        <li class="item" data-category="drink">水 (Drink)</li>
    </ul>
</body>
</html>
```
</details>
