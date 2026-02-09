# 第 3 章：DOM 操作與資料存取 (DOM Manipulation & Data)

## 本章目標
- 學會讀取與修改網頁內容 (`text`, `html`, `val`)。
- 分辨 `attr()` 與 `prop()` 的差異（重要面試題）。
- 掌握現代前端必備的 `data-*` 屬性與 `.data()` 快取機制。
- 靈活操作 CSS 類別與 DOM 節點的新增刪除。
- **理解盒模型 (Box Model) 的尺寸計算**。

---

## 3.1 內容讀取與修改 (Content)

jQuery 提供了三個主要方法來操作元素的內容。這些方法都支援「讀取 (Get)」與「修改 (Set)」兩種模式。

### 1. `text()` - 純文字操作
此方法用於處理元素內的「純文字」。當讀取時，它會過濾掉所有標籤；當寫入時，它會將 HTML 標籤轉為普通文字，不會被瀏覽器解析。

**情境 HTML：**
```html
<p id="msg">Hello <b>World</b></p>
```

**範例操作：**
```javascript
// A. 讀取 (Get): 不傳入參數
var content = $("#msg").text();
// 結果: 變數 content 的值為 "Hello World" (<b> 標籤被過濾掉了)

// B. 修改 (Set): 傳入字串參數
$("#msg").text("Hello <i>jQuery</i>");
```

**執行後結果：**
- **畫面顯示**：Hello <i>jQuery</i> (直接看到斜體標籤文字，沒有變斜體)
- **HTML 結構**：`<p id="msg">Hello &lt;i&gt;jQuery&lt;/i&gt;</p>`

---

### 2. `html()` - HTML 結構操作
此方法用於處理元素內的「HTML 結構」。它會保留標籤的完整性，當寫入包含標籤的字串時，瀏覽器會將其解析為實際的 DOM 元素。

**情境 HTML：**
```html
<div id="box">原始 <b>內容</b></div>
```

**範例操作：**
```javascript
// A. 讀取 (Get): 不傳入參數
var structure = $("#box").html();
// 結果: 變數 structure 的值為 "原始 <b>內容</b>" (包含標籤)

// B. 修改 (Set): 傳入包含標籤的字串
$("#box").html("<h1>大標題</h1>");
```

**執行後結果：**
- **畫面顯示**：顯示一個粗體巨大的「大標題」
- **HTML 結構**：`<div id="box"><h1>大標題</h1></div>`

---

### 3. `val()` - 表單值操作
此方法專門用於表單控制項，如 `<input>`, `<select>`, `<textarea>`。

#### 情境 A：一般輸入框 (Input Text)
```html
<input type="text" id="user" value="Guest">
```
```javascript
// 讀取
var name = $("#user").val(); // "Guest"
// 修改
$("#user").val("Admin");     // 輸入框內容變為 "Admin"
```

#### 情境 B：單選下拉選單 (Single Select)
```html
<select id="city">
    <option value="TP">Taipei</option>
    <option value="NY">New York</option>
</select>
```
```javascript
// 讀取 (讀取目前被選中的 option 的 value)
var selectedCity = $("#city").val(); // 預設是 "TP" (字串)

// 修改 (設定選中項目)
$("#city").val("NY"); // 自動將 New York 設為選中狀態
```

#### 情境 C：多選下拉選單 (Multiple Select)
**注意：** 當 `<select>` 加上 `multiple` 屬性時，`val()` 回傳的是**陣列 (Array)**。

```html
<select id="skills" multiple>
    <option value="html">HTML</option>
    <option value="css">CSS</option>
    <option value="js">JavaScript</option>
</select>
```
```javascript
// 假設使用者選了 HTML 和 CSS
var mySkills = $("#skills").val(); 
// 結果: ["html", "css"] (陣列)

// 設定多選 (傳入陣列)
$("#skills").val(["html", "js"]); // 同時選中 HTML 和 JavaScript
```

#### 情境 D：多行輸入 (Textarea)
```html
<textarea id="memo">預設文字</textarea>
```
```javascript
// 注意：textarea 的內容雖然在 HTML 標籤之間，但 jQuery 統一用 val() 存取
var memo = $("#memo").val(); 
$("#memo").val("新內容...");
```

---

### [隨堂小測驗 1]
**準備工作：** 請複製以下 HTML。
```html
<div id="content">原本的內容</div>
<select id="role">
    <option value="user">一般用戶</option>
    <option value="admin">管理員</option>
</select>
```

**任務：**
1. 將 `#content` 的內容修改為 `<h1>大標題</h1>` (要讓標籤生效)：
   ```javascript
   $("#content").____("<h1>大標題</h1>");
   ```
2. 將 `#role` 下拉選單設定為「管理員」 (value 為 admin)：
   ```javascript
   $("#role").____("admin");
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. 使用 html() 讓標籤生效
$("#content").html("<h1>大標題</h1>");

// 2. 表單值用 val()
$("#role").val("admin");
```
</details>

---

## 3.2 屬性操作 (Attributes & Properties)

這是 jQuery 中最容易搞混的地方：`attr()` vs `prop()`。

### 背景知識：為什麼會有這兩個？
這不是 jQuery 故意找麻煩，而是瀏覽器底層運作機制本就如此。
- **Attribute (屬性)**：寫在 HTML 原始碼裡的東西。例如 `<input value="abc">`，這裡的 Attribute value 永遠是 "abc"。
- **Property (特性)**：DOM 物件在記憶體中的狀態。當使用者在輸入框打字變成 "abcdef" 時，DOM 的 Property value 會變，但 HTML Attribute 還是 "abc"。

#### 混用的嚴重後果 (重要！)
在處理 **Checkbox** 或 **Radio** 時，混用這兩者會導致 UI 顯示與實際數值脫鉤。

**情境 HTML：**
```html
<!-- 初始狀態：未勾選 -->
<input type="checkbox" id="testBox"> 測試核取方塊
<button id="checkBtn">查看狀態</button>
```

**範例操作 (當使用者手動勾選後)：**
```javascript
$("#checkBtn").click(function(){
    // 錯誤示範：使用 attr 抓取動態狀態
    console.log("attr:", $("#testBox").attr("checked")); 
    // 結果: undefined (即便勾選了，HTML 原始碼沒變，attr 就抓不到)

    // 正確示範：使用 prop 抓取記憶體中的狀態
    console.log("prop:", $("#testBox").prop("checked")); 
    // 結果: true (正確反應當前勾選狀態)
});
```

**結論：涉及「狀態」的操作，請死守 `.prop()`。**

---

### 1. 一般屬性 (`attr`)
此方法用於處理 HTML 標籤上**「寫在原始碼裡」**的屬性，如 `href`, `src`, `title`, `id`, `alt`, `data-*`。

**情境 HTML：**
```html
<img id="logo" src="default.jpg" title="預設圖片">
<a id="link" href="http://google.com">Google</a>
```

**範例操作：**
```javascript
// A. 讀取 (Get)
var link = $("#link").attr("href");
// 結果: 變數 link 的值為 "http://google.com"

// B. 修改 (Set)
$("#logo").attr("src", "new-logo.png");

// C. 移除 (Remove)
$("#link").removeAttr("href");
```

**執行後結果：**
- **HTML 結構 (修改後)**：`<img id="logo" src="new-logo.png" ...>`
- **HTML 結構 (移除後)**：`<a id="link">Google</a>` (變成了無法點擊的連結)

---

### 2. 狀態屬性 (`prop`)
此方法用於處理 DOM 元素的**「當前狀態 (Current State)」**，特別是那些**布林值 (Boolean)** 的屬性，如 `checked`, `selected`, `disabled`。

> **黃金法則：**
> - 只要是涉及 true/false 的狀態（如勾選、禁用），一律用 `.prop()`。
> - 其他看得見的字串屬性，用 `.attr()`。

**情境 HTML：**
```html
<input type="checkbox" id="agree"> 我同意
<button id="btn" disabled>送出</button>
```

**範例操作：**
```javascript
// A. 讀取 (Get)
var isDisabled = $("#btn").prop("disabled");
// 結果: true (因為 HTML 裡有寫 disabled)

// B. 修改 (Set) - 勾選 Checkbox
$("#agree").prop("checked", true);

// C. 修改 (Set) - 啟用按鈕 (移除禁用狀態)
$("#btn").prop("disabled", false);
```

**執行後結果：**
- **畫面顯示**：核取方塊被勾選了，按鈕變回可點擊狀態（不再灰階）。
- **DOM 狀態**：元素的 `checked` 屬性變為 `true`，`disabled` 屬性變為 `false`。

---

### [隨堂小測驗 2]
**準備工作：** 請複製以下 HTML。
```html
<img id="banner" src="old.jpg" title="舊圖">
<input type="checkbox" id="notify"> 接收通知
```

**任務：**
1. 將 `#banner` 的 `title` 屬性修改為 "新圖片"：
   ```javascript
   $("#banner").____("title", "新圖片");
   ```
2. 將 `#notify` 核取方塊設定為**已勾選**狀態：
   ```javascript
   $("#notify").____("checked", true);
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. 一般屬性用 attr
$("#banner").attr("title", "新圖片");

// 2. 狀態屬性用 prop
$("#notify").prop("checked", true);
```
</details>

---

## 3.3 資料緩存 (Data Caching)

HTML5 允許我們使用 `data-*` 屬性在標籤上儲存自定義資料，而 jQuery 的 `.data()` 方法則提供了一個高效的介面來讀寫這些資料，並且支援**自動型別轉換**。

### `data()` 的自動轉型
這是 `.data()` 與 `.attr()` 最大的差別：
- `.attr("data-xxx")`：永遠回傳 **字串 (String)**。
- `.data("xxx")`：會自動判斷內容，轉為 **數字 (Number)**、**布林 (Boolean)** 或 **物件 (Object)**。

**情境 HTML：**
```html
<div id="product" 
     data-id="101" 
     data-price="500" 
     data-vip="true">
    商品資訊
</div>
```

**範例操作：**
```javascript
// A. 使用 attr (回傳字串)
var attrPrice = $("#product").attr("data-price");
// 結果: "500" (String)

// B. 使用 data (自動轉型為數字)
var dataPrice = $("#product").data("price");
// 結果: 500 (Number) -> 可以直接拿來計算！

// C. 讀取布林值
var isVip = $("#product").data("vip"); 
// 結果: true (Boolean) -> 可以直接放在 if() 裡判斷

// D. 修改資料 (僅存於記憶體，不會改寫 HTML 屬性)
$("#product").data("price", 600);
```

**執行後結果：**
- **記憶體狀態**：`price` 的值變為 600。
- **HTML 原始碼**：`<div data-price="500" ...>` (注意：HTML 屬性本身**不會變**，這就是「緩存」的意思，效能較高)。

---

### [隨堂小測驗 3]
**準備工作：** 請複製以下 HTML。
```html
<div id="user-card" data-age="25" data-admin="true"></div>
```

**任務：**
1. 使用 `.data()` 讀取 `data-age`，並將其加 5 (因為它會自動轉成數字)：
   ```javascript
   var newAge = $("#user-card").____("age") + 5;
   console.log(newAge); // 應該印出 30
   ```
2. 檢查使用者是否為管理員 (`data-admin`)：
   ```javascript
   var isAdmin = $("#user-card").____("admin");
   console.log(isAdmin); // 應該印出 true
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. data("age") 自動轉為數字
var newAge = $("#user-card").data("age") + 5;

// 2. data("admin") 自動轉為布林值
var isAdmin = $("#user-card").data("admin");
```
</details>

---

## 3.4 CSS 類別與樣式 (Classes & Styles)

操作樣式最好的方法不是直接改 CSS (`.css`)，而是切換 Class (`.addClass`)，讓 CSS 負責樣式，JS 負責邏輯。

### 1. 類別操作方法 (推薦)
這是最符合「關注點分離 (Separation of Concerns)」的作法。

**情境 HTML：**
```html
<style>
    .highlight { background: yellow; font-weight: bold; }
</style>
<p id="desc">這是一段文字</p>
```

**範例操作：**
```javascript
// A. 加入類別 (Add)
$("#desc").addClass("highlight");
// 結果: <p id="desc" class="highlight"> (背景變黃)

// B. 移除類別 (Remove)
$("#desc").removeClass("highlight");
// 結果: <p id="desc" class=""> (變回原樣)

// C. 切換類別 (Toggle)
// 如果有 -> 移除；如果沒有 -> 加入
$("#desc").toggleClass("highlight");

// D. 判斷是否有類別 (Has)
var hasHighlight = $("#desc").hasClass("highlight"); 
// 結果: true 或 false
```

---

### 2. CSS 直接操作 (不推薦大量使用)
只有在處理「動態數值」（如拖拉位置、進度條寬度、隨機顏色）時，才建議用 `.css()`。

**情境 HTML：**
```html
<div id="bar" style="width: 10px; background: blue;"></div>
```

**範例操作：**
```javascript
// A. 設定單一屬性
$("#bar").css("width", "500px");
// 結果: div 變寬了

// B. 設定多個屬性 (傳入物件)
$("#bar").css({
    "opacity": "0.5",
    "margin-top": "20px"
});

// C. 讀取屬性值 (Get)
var currentWidth = $("#bar").css("width");
// 結果: "500px" (字串，包含單位)
```

---

### [隨堂小測驗 4]
**準備工作：** 請複製以下 HTML。
```html
<style> .dark-mode { background: #333; color: white; } </style>
<body id="myBody">
    <h1>夜間模式測試</h1>
    <button id="toggleBtn">切換</button>
</body>
```

**任務：**
1. 點擊按鈕時，切換 `body` 的 `dark-mode` 類別 (原本沒有變有，有變沒有)：
   ```javascript
   $("#toggleBtn").click(function(){
       $("#myBody").____("dark-mode");
   });
   ```

<details>
<summary>查看解答</summary>

```javascript
$("#toggleBtn").click(function(){
    // 使用 toggleClass 自動切換
    $("#myBody").toggleClass("dark-mode");
});
```
</details>

---

## 3.5 節點操作 (DOM Insertion & Removal)

如何在網頁上動態新增或刪除元素？

### 1. 新增元素 (內部插入)
- **`append()`**: 加在內容的**最後面**。
- **`prepend()`**: 加在內容的**最前面**。

**情境 HTML：**
```html
<ul id="list">
    <li>Item 1</li>
</ul>
```

**範例操作：**
```javascript
// A. 加在後面
$("#list").append("<li>Item 2 (Append)</li>");

// B. 加在前面
$("#list").prepend("<li>Item 0 (Prepend)</li>");
```

**執行後結果：**
```html
<ul id="list">
    <li>Item 0 (Prepend)</li> <!-- 跑到最上面 -->
    <li>Item 1</li>           <!-- 原本的 -->
    <li>Item 2 (Append)</li>  <!-- 跑到最下面 -->
</ul>
```

### 2. 刪除元素
- **`remove()`**: 刪除元素本身（連同子元素），該標籤會從網頁上完全消失。
- **`empty()`**: 僅清空元素的內容（保留外殼），該標籤還在，只是變空了。

**情境 HTML：**
```html
<div id="box1"><p>我是 Box 1</p></div>
<div id="box2"><p>我是 Box 2</p></div>
```

**範例操作：**
```javascript
// A. 清空內容
$("#box1").empty();

// B. 完全移除
$("#box2").remove();
```

**執行後結果：**
- **Box 1**：HTML 變為 `<div id="box1"></div>` (還活著，但空了)。
- **Box 2**：HTML 完全消失，DOM 樹中找不到它了。

---

### [隨堂小測驗 5]
**準備工作：** 請複製以下 HTML。
```html
<div id="container">
    <p>舊的段落</p>
</div>
<button id="addBtn">新增</button>
<button id="clearBtn">清空</button>
```

**任務：**
1. 點擊「新增」按鈕，在 `#container` 內部**最後面**加入 `<p>新段落</p>`：
   ```javascript
   $("#container").____("<p>新段落</p>");
   ```
2. 點擊「清空」按鈕，移除 `#container` 裡面的所有內容 (但保留 `#container` div 本身)：
   ```javascript
   $("#container").____();
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. append
$("#container").append("<p>新段落</p>");

// 2. empty
$("#container").empty();
```
</details>

---

## 3.6 尺寸與位置 (Dimensions & Position)

這是前端處理排版與特效時最關鍵的章節。jQuery 提供了非常直觀的方法來處理盒模型（Box Model）的各種數值。

### 1. 基礎寬度與高度 (width / height)
這組方法對應 CSS 的 `width` 與 `height`。支援讀取與設定。

**情境 HTML：**
```html
<div id="rect" style="width: 200px; height: 100px; background: red;"></div>
```

**範例操作：**
```javascript
// A. 讀取 (Get)
var w = $("#rect").width();  // 200
var h = $("#rect").height(); // 100

// B. 設定 (Set)
$("#rect").width(300);       // 寬度變為 300px
$("#rect").height("150px");  // 高度變為 150px
```

---

### 2. 盒模型尺寸計算 (Inner / Outer)
在實務中，我們常需要計算包含內距（Padding）或邊框（Border）的尺寸。

**情境 HTML 與 CSS：**
```html
<style>
    #box {
        width: 100px; padding: 10px; border: 5px solid; margin: 20px;
    }
</style>
<div id="box">Target</div>
```

**範例操作與計算：**
```javascript
// A. 僅內容
$("#box").width();      // 100

// B. 內容 + Padding
$("#box").innerWidth(); // 100 + 10 + 10 = 120

// C. 內容 + Padding + Border (最常用)
$("#box").outerWidth(); // 120 + 5 + 5 = 130

// D. 內容 + Padding + Border + Margin
$("#box").outerWidth(true); // 130 + 20 + 20 = 170
```

---

### 3. 元素位置 (offset / position)
取得座標是製作動態效果（如浮動選單、自定義 Tooltip）的核心。這涉及到 CSS 的「定位上下文」觀念。

#### A. `offset()` - 相對於「整份文件 (Document)」
不管元素的父層是誰，它回傳的是元素距離**整個網頁最左上角 (0,0)** 的座標。
- **特點**：此方法支援「讀取」與「寫入」。
- **用途**：當你需要把一個彈出視窗（如廣告、對話框）精準放在某個按鈕旁邊時。

#### B. `position()` - 相對於「最近的定位父元素 (Offset Parent)」
它回傳的是元素相對於其**最近的一個具有 `position: relative/absolute/fixed` 屬性的父元素**的距離。
- **特點**：此方法**僅支援讀取**，不可寫入。
- **用途**：製作容器內部的動畫，或處理子元素相對於容器的位移。

**情境 HTML 與 CSS：**
```html
<style>
    /* 父容器有定位，且距離網頁頂部 100px */
    #container { 
        position: relative; 
        margin-top: 100px; 
        padding: 50px; 
        background: #eee;
    }
    /* 子元素定位在父容器內部座標 (30, 20) */
    #target { 
        position: absolute; 
        top: 30px; 
        left: 20px; 
        width: 100px; 
        height: 50px; 
        background: blue;
    }
</style>

<div id="container">
    <div id="target">目標</div>
</div>
```

**範例操作：**
```javascript
// 1. 取得絕對座標 (Document)
var off = $("#target").offset(); 
// 結果: { top: 130, left: 20 } 
// (父層的 100px margin + 子層的 30px top)

// 2. 取得相對座標 (Offset Parent)
var pos = $("#target").position();
// 結果: { top: 30, left: 20 }
// (直接反應 CSS 定位的數值)

// 3. 設定絕對座標 (讓目標飛到網頁的最左上角)
$("#target").offset({ top: 0, left: 0 });
```

**執行後結果：**
- `offset()` 會幫你把複雜的巢狀結構與 Margin 全部加總，給出最終物理座標。
- `position()` 則回傳元素在定位系統中的純粹數值。

---

---

### 4. 捲軸位置 (scrollTop)
`scrollTop()` 用於讀取或設定被選元素「垂直捲軸」的偏移量。在實務中，我們最常對 `$(window)` (整個瀏覽器視窗) 或具有 `overflow: scroll` 的容器進行操作。

#### 實戰應用 A：製作「回到頂部」按鈕
這是最常見的功能。當使用者向下捲動超過一定距離時顯示按鈕，點擊後回到最上方。

**情境 HTML：**
```html
<!-- 模擬一個超長的內容 -->
<div style="height: 2000px;">向下捲動看看...</div>
<!-- 回到頂部按鈕 (預設隱藏) -->
<button id="backToTop" style="position: fixed; bottom: 20px; right: 20px; display: none;">
    Top ↑
</button>
```

**範例操作：**
```javascript
// 1. 偵測視窗捲動事件
$(window).scroll(function() {
    // 讀取當前視窗捲動了多少
    var scrollPos = $(window).scrollTop();
    
    if (scrollPos > 300) {
        $("#backToTop").fadeIn(); // 捲動超過 300px 就顯示
    } else {
        $("#backToTop").fadeOut(); // 否則隱藏
    }
});

// 2. 點擊按鈕回到頂部
$("#backToTop").click(function() {
    $(window).scrollTop(0); // 直接設定捲軸位置為 0
});
```

---

#### 實戰應用 B：選單錨點跳轉 (Anchor Jump)
點擊導覽列，讓網頁自動捲動到指定的內容區塊。

**情境 HTML：**
```html
<nav>
    <button class="nav-btn" data-target="#news">最新消息</button>
    <button class="nav-btn" data-target="#contact">聯絡我們</button>
</nav>

<div style="height: 800px;">...中間內容...</div>
<div id="news" style="height: 500px; background: #ffe;">最新消息區塊</div>
<div id="contact" style="height: 500px; background: #eef;">聯絡我們區塊</div>
```

**範例操作：**
```javascript
$(".nav-btn").click(function() {
    // 1. 取得目標區塊的 ID (例如 "#news")
    var targetId = $(this).data("target");
    
    // 2. 取得該目標區塊距離網頁頂部的位置
    var targetOffset = $(targetId).offset().top;
    
    // 3. 讓視窗捲動到該位置
    $(window).scrollTop(targetOffset);
});
```

**執行後結果：**
- 使用者點擊按鈕後，瀏覽器會精準地跳轉到目標區塊的最上方。
- **進階提示**：搭配第 5 章的 `animate()`，可以實現平滑的捲動動畫，而非瞬間跳轉。

---

---

### [隨堂小測驗 6]
**準備工作：** 請複製以下代碼。
```html
<style>
    #test { width: 50px; padding: 5px; border: 2px solid; }
</style>
<div id="test"></div>
```

**任務：**
1. 請問 `$("#test").outerWidth()` 的回傳值是多少？
2. 如果執行 `$("#test").width(100)`，請問畫面的總寬度（包含 Padding/Border）會變成多少？

<details>
<summary>查看解答</summary>

1. **64** (50內容 + 5*2內距 + 2*2邊框)。
2. **114** (設定後的 100內容 + 5*2內距 + 2*2邊框)。
</details>

---

## 3.7 綜合課後練習 (Exercise)

**專案：簡易待辦事項 (Todo List)**

**任務目標：**
1. 建立一個 `index.html`，並貼上以下結構：
```html
<input type="text" id="taskInput" placeholder="輸入待辦事項...">
<button id="addBtn">Add</button>
<ul id="taskList">
    <li>預設項目 1 (點我試試)</li>
</ul>
<style>
    .completed { text-decoration: line-through; color: gray; }
</style>
```

2. 撰寫 jQuery 程式碼完成以下功能：
   - **功能 A**：點擊 Add 按鈕時，讀取輸入框的值 (`val`)。
     - 如果是空的，跳出 `alert` 警告。
     - 如果有值，使用 `append` 新增到 `#taskList` 中。
   - **功能 B**：新增完畢後，清空輸入框的值 (`val("")`)。
   - **功能 C**：點擊列表中的 `li` 時，切換 `completed` 類別 (`toggleClass`)。

> **注意：** 你可能會發現**新增加的項目點擊後沒有刪除線效果**。這是正常的！因為你的 `click` 事件是在頁面載入時綁定的，當時新項目還不存在。我們會在**下一章**教你如何解決這個問題 (Event Delegation)。

---

### 練習解答 (Solution)

<details>
<summary>查看解答程式碼</summary>

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <title>Todo List 練習</title>
    <style>
        .completed { text-decoration: line-through; color: gray; }
    </style>
    <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
    <script>
        $(function() {
            $("#addBtn").click(function() {
                // 1. 讀取值
                var task = $("#taskInput").val();
                
                // 檢查是否為空
                if (task === "") {
                    alert("請輸入內容！");
                    return;
                }
                
                // 2. 新增到列表
                $("#taskList").append("<li>" + task + "</li>");
                
                // 3. 清空輸入框
                $("#taskInput").val("");
            });

            // 4. 點擊項目畫刪除線 (僅對既有項目有效)
            $("li").click(function(){
                $(this).toggleClass("completed");
            });
        });
    </script>
</head>
<body>
    <input type="text" id="taskInput" placeholder="輸入待辦事項...">
    <button id="addBtn">Add</button>
    
    <ul id="taskList">
        <li>預設項目 1 (點我試試)</li>
    </ul>
</body>
</html>
```
</details>