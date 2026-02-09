# 第 4 章：事件處理 (Events)

## 本章目標
- 建立網頁互動的核心：讓網頁對使用者的操作做出反應。
- 掌握滑鼠、表單與鍵盤常見事件。
- 理解並解決「動態生成元素」無法綁定事件的問題（事件委派）。
- 學習如何主動觸發事件。

## 教學大綱
- [x] 4.1 常見事件
- [x] 4.2 標準綁定與解綁
- [x] 4.3 事件委派 (Event Delegation) 【核心難點】
- [x] 4.4 事件物件與控制 (The Event Object)
- [ ] 4.5 主動觸發事件 (Trigger)
- [ ] 4.6 實作練習

---

## 4.1 常見事件 (Common Events)

事件是當使用者與網頁互動時，瀏覽器所發出的「訊號」。jQuery 讓我們能輕鬆監聽這些訊號並執行對應的函式 (Event Handler)。

### 1. 滑鼠事件 (Mouse Events)
這是最直觀的互動方式。

| 事件名稱 | 觸發時機 |
| :--- | :--- |
| `click` | 當按鍵被「單擊」時。 |
| `dblclick` | 當按鍵被「連點兩下」時。 |
| `mouseenter` | 當滑鼠指標「進入」元素範圍時。 |
| `mouseleave` | 當滑鼠指標「離開」元素範圍時。 |

**情境 HTML：**
```html
<button id="btnClick">單擊測試</button>
<button id="btnDbl">連點兩下測試</button>
<div id="hoverBox" style="width:100px; height:100px; background:gray; color:white; padding:10px;">
    Hover Me
</div>
```

**範例操作：**
```javascript
// A. 單擊
$("#btnClick").click(function() {
    $(this).text("已點擊");
});

// B. 雙擊
$("#btnDbl").dblclick(function() {
    $(this).css("background", "orange");
});

// C. 滑鼠進入與離開 (常用於製作按鈕變色效果)
$("#hoverBox").mouseenter(function() {
    $(this).css("background", "blue").text("進入範圍");
}).mouseleave(function() {
    $(this).css("background", "gray").text("Hover Me");
});
```

---

## 4.2 標準綁定與解綁 (On, Off, One)

雖然 4.1 教的 `.click()` 很方便，但在現代 jQuery 開發中，我們更推薦使用 **`.on()`**。

### 1. 為何推薦使用 `.on()`？
- **語法統一**：無論是滑鼠、鍵盤還是自定義事件，通通用 `.on()`。
- **支援多重事件**：可以一次綁定多個事件（使用物件語法）。
- **動態元素支援 (關鍵優勢)**：
    - 使用 `.click()` 綁定事件時，只能綁定在「目前已經存在」的元素。
    - `.on()` 配合「事件委派」語法，可以處理未來產生的元素。

---

## 4.3 事件委派 (Event Delegation)

這是 jQuery 中最重要、也是初學者最容易卡關的進階觀念。

### 1. 為什麼需要委派？
JavaScript 的事件綁定是在「程式執行那一刻」完成的。如果你後來才透過 `append()` 新增元素，這些新同學是沒被註冊到事件的。

### 2. 解決方案
我們不把事件直接綁在會變動的子元素上，而是綁在一個**「本來就存在、且不會消失」**的父元素上。

**語法：** `$(靜態父元素).on("事件名稱", "動態子元素選擇器", function() { ... });`

**情境 HTML：**
```html
<button id="addBtn">新增項目</button>
<ul id="taskList">
    <li>原始項目</li>
</ul>
```

**範例操作：**
```javascript
// 將事件綁在 #taskList (老爸)，監控 li (小孩)
$("#taskList").on("click", "li", function() {
    $(this).css("color", "red"); 
});

$("#addBtn").click(function() {
    $("#taskList").append("<li>我是新來的，我也會變色！</li>");
});
```

---

## 4.4 事件物件與控制 (The Event Object)

當事件被觸發時，jQuery 會自動傳遞一個「事件物件」給處理函式。這個物件（通常命名為 `e`）包含了所有關於該事件的詳細資訊。

### 1. 取得滑鼠座標
**情境 HTML：**
```html
<div id="coordBox" style="width:200px; height:100px; background:#eee; border:1px solid #ccc; text-align:center; line-height:100px;">
    在此移動滑鼠
</div>
<p id="posInfo">座標：(0, 0)</p>
```
**範例操作：**
```javascript
$("#coordBox").mousemove(function(e) {
    // e.pageX 和 e.pageY 是滑鼠在頁面上的座標
    $("#posInfo").text("座標：(" + e.pageX + ", " + e.pageY + ")");
});
```

### 2. 阻止預設行為 `e.preventDefault()`
用來攔截 HTML 元素原本的行為（如：點擊連結跳轉、表單送出）。

**情境 HTML：**
```html
<a href="https://google.com" id="preventLink">點我不會跳轉</a>
```
**範例操作：**
```javascript
$("#preventLink").click(function(e) {
    e.preventDefault(); // 攔截跳轉
    alert("跳轉已被阻止！");
});
```

### 3. 阻止事件冒泡 `e.stopPropagation()`
阻止點擊訊號繼續向父元素傳遞。

**情境 HTML：**
```html
<div id="outer" style="padding:50px; background:gray;">
    我是父層
    <button id="inner">我是子層按鈕</button>
</div>
```
**範例操作：**
```javascript
$("#outer").click(function() {
    alert("點到了父層");
});

$("#inner").click(function(e) {
    e.stopPropagation(); // 阻止訊號傳給父層
    alert("點到了子層按鈕，訊號停止傳遞");
});
```

---

### [隨堂小測驗 4]
**準備工作：** 請複製以下 HTML。
```html
<div id="wrapper" style="padding:20px; background:lightblue;">
    <a href="delete.php" class="delBtn">刪除資料</a>
</div>
```
**任務：**
1. 為 `.delBtn` 綁定點擊事件。
2. 執行 `e.preventDefault()` 確保不會跳轉到 `delete.php`。
3. 執行 `e.stopPropagation()` 確保訊號不會傳到 `#wrapper`。
4. 點擊後跳出 `confirm("確定刪除？")`。

<details>
<summary>查看解答</summary>

```javascript
$(".delBtn").on("click", function(e) {
    e.preventDefault();
    e.stopPropagation();
    if (confirm("確定刪除？")) {
        alert("執行中...");
    }
});
```
</details>

---

## 4.5 主動觸發事件 (Trigger)
- [ ] `trigger('click')`：模擬使用者點擊
- [ ] 應用場景：頁面載入時自動執行搜尋、連動選單觸發

## 4.6 實作練習
- [ ] 練習 4：
    1. 實作一個動態新增按鈕的功能。
    2. 使用 **事件委派** 確保新生成的按鈕也能彈出 alert。
    3. 實作 "點擊背景關閉視窗" 功能 (利用 `stopPropagation` 防止點擊視窗內容時也關閉)。