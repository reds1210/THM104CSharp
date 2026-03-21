# 第 4 章：事件處理 (Events)

## 本章目標
- 掌握滑鼠與鍵盤的基礎事件監聽。
- 理解 `.on()` 綁定與 `.off()` 解綁的時機。
- **掌握事件委派 (Event Delegation) 解決動態元素失效問題（必考重點）**。
- 學會使用事件物件 (`event`) 控制瀏覽器預設行為與冒泡。
- 靈活運用 `trigger()` 主動觸發網頁交互。

---

## 4.1 基礎事件監聽 (Basic Events)

事件是使用者與網頁互動的瞬間（如點擊、打字）。jQuery 提供了直觀的方法來捕捉這些訊號。

### 1. 滑鼠點擊與進入 (Mouse)
這是最常用的互動，包含單擊、雙擊與滑鼠移入移出。

**情境 HTML：**
```html
<button id="btn">按鈕</button>
<div id="box" style="width:100px; height:100px; background:gray;"></div>
```

**範例操作：**
```javascript
// A. 單擊 (Click)
$("#btn").click(function() {
    $(this).text("已點擊");
});

// B. 移入與移出 (MouseEnter/Leave)
$("#box").mouseenter(function() {
    $(this).css("background", "red");
}).mouseleave(function() {
    $(this).css("background", "gray");
});
```

**執行後結果：**
- **點擊按鈕**：按鈕文字會變為「已點擊」。
- **滑鼠移入方塊**：背景色變紅；移出則恢復灰色。

---

### 2. 鍵盤與表單 (Keyboard & Form)
用於處理輸入框的文字輸入與焦點狀態。

**情境 HTML：**
```html
<input type="text" id="user" placeholder="請輸入姓名">
```

**範例操作：**
```javascript
// A. 取得焦點 (Focus)
$("#user").focus(function() {
    $(this).css("border", "2px solid blue");
});

// B. 鍵盤壓下 (Keydown)
$("#user").keydown(function(e) {
    console.log("正在輸入，鍵盤代碼：" + e.which);
});
```

**執行後結果：**
- **點擊輸入框**：外框會變為藍色粗線。
- **按下任意鍵**：控制台 (Console) 會印出對應的按鍵數字代碼。

---

### [隨堂小測驗 1]
**任務：**
1. 讓 `#imgLogo` 在雙擊 (`dblclick`) 時消失 (`hide`)：
   ```javascript
   $("#imgLogo").____(function() {
       $(this).hide();
   });
   ```
2. 當使用者離開 (`blur`) `#email` 輸入框時，跳出 `alert("離開輸入框")`：
   ```javascript
   $("#email").____(function() {
       alert("離開輸入框");
   });
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. dblclick
$("#imgLogo").dblclick(function() { $(this).hide(); });

// 2. blur
$("#email").blur(function() { alert("離開輸入框"); });
```
</details>

---

## 4.2 標準綁定與解綁 (On & Off)

在現代 jQuery 開發中，我們推薦使用 `.on()` 來取代舊式的 `.click()`，因為它更具靈活性且語法統一。

### 1. 多重綁定 (On)
你可以用一個 `.on()` 同時處理多個事件，減少重複代碼。

**範例操作：**
```javascript
// 使用物件語法一次綁定
$("#btn").on({
    "click": function() { console.log("點擊"); },
    "mouseenter": function() { $(this).css("opacity", 0.5); },
    "mouseleave": function() { $(this).css("opacity", 1); }
});
```

### 2. 移除綁定 (Off)
當某個功能在特定條件下（如：遊戲結束、表單已送出）不再需要反應時。

**範例操作：**
```javascript
// 移除按鈕上所有的點擊事件
$("#btn").off("click");
```

---

## 4.3 事件委派 (Event Delegation)

這是 jQuery 中最重要、也是初學者最容易卡關的進階觀念。

### 背景知識：為什麼需要委派？
JavaScript 的事件綁定是在「程式執行那一刻」完成的。如果你後來才透過 `append()` 新增元素，這些「新同學」是不會帶有原本綁定的事件的。

> **黃金法則：**
> 只要是會「動態新增」的元素，事件必須綁在它的**靜態父元素**（原本就存在 HTML 裡的）身上。

**情境 HTML：**
```html
<button id="addBtn">新增項目</button>
<ul id="list">
    <li>原始項目 (點我會變紅)</li>
</ul>
```

**範例操作：**
```javascript
// 正確做法：委派給父層 #list，監控底下的 li
$("#list").on("click", "li", function() {
    $(this).css("color", "red");
});

// 點擊按鈕新增 li
$("#addBtn").click(function() {
    $("#list").append("<li>我是新來的，我也會變紅！</li>");
});
```

**執行後結果：**
- **原始項目**：點擊後文字變紅。
- **新項目**：因為使用了「事件委派」，即便是在 `.on()` 執行後才產生的項目，點擊依然有效。

---

### [隨堂小測驗 2]
**任務：**
1. 修正以下代碼，讓動態新增的 `.del-btn` 也能觸發事件：
   ```javascript
   // 原本無效的寫法
   // $(".del-btn").click(function() { $(this).parent().remove(); });

   // 修正為委派寫法 (假設父層是 #container)
   $("#container").____("click", "____", function() {
       $(this).parent().remove();
   });
   ```

<details>
<summary>查看解答</summary>

```javascript
// 使用 on 進行委派
$("#container").on("click", ".del-btn", function() {
    $(this).parent().remove();
});
```
</details>

---

## 4.4 事件物件與控制 (Event Object)

當事件被觸發時，jQuery 會自動傳遞一個包含所有詳細資訊的「事件物件」給處理函式。

### 1. 阻止預設行為 (preventDefault)
用於攔截 HTML 元素原本的行為（如：點擊連結跳轉、表單送出）。

**情境 HTML：**
```html
<a href="https://google.com" id="noGo">點我不會跳轉</a>
```

**範例操作：**
```javascript
$("#noGo").click(function(e) {
    e.preventDefault(); // 攔截跳轉訊號
    alert("跳轉已被攔截！");
});
```

### 2. 阻止事件冒泡 (stopPropagation)
防止點擊訊號繼續向父元素傳遞，造成誤觸發。

**情境 HTML：**
```html
<div id="parent" style="padding:50px; background:gray;">
    父層
    <button id="child">子層按鈕</button>
</div>
```

**範例操作：**
```javascript
$("#parent").click(function() { alert("點到父層"); });

$("#child").click(function(e) {
    e.stopPropagation(); // 停止訊號向上傳遞
    alert("點到子層，父層不會反應");
});
```

---

## 4.5 主動觸發事件 (Trigger)

有時候我們希望「程式主動執行」某個按鈕的功能，而不需要使用者真的去點。

**範例操作：**
```javascript
// 假設有一個隱藏的檔案上傳按鈕
$("#uploadBtn").click(function() {
    console.log("開啟檔案視窗...");
});

// 當點擊另一個漂亮的圖片時，主動觸發 uploadBtn 的點擊
$("#prettyPic").click(function() {
    $("#uploadBtn").trigger("click");
});
```

---

## 4.6 綜合課後練習 (Exercise)

**專案：標籤管理系統 (Tag Manager)**

**任務目標：**
1. 建立一個 `index.html`，並貼上以下結構：
```html
<input type="text" id="tagInput" placeholder="輸入文字按下 Enter...">
<div id="tagBox" style="margin-top: 20px;">
    <!-- 標籤顯示區 -->
</div>
<style>
    .tag { display: inline-block; padding: 5px 15px; background: #007bff; color: white; border-radius: 20px; margin: 5px; cursor: pointer; }
</style>
```

2. 撰寫 jQuery 程式碼完成以下功能：
   - **功能 A**：在輸入框輸入文字並按下 **Enter** 鍵時 (提示：鍵盤代碼 `e.which` 為 13)，讀取文字。
   - **功能 B**：如果是空的，不執行；如果有值，使用 `append` 將其包裝在 `<span class="tag">` 中新增到 `#tagBox`。
   - **功能 C**：新增完畢後，清空輸入框的值。
   - **功能 D**：點擊任何產生的標籤時，將該標籤刪除 (`remove`)。**注意：此功能必須使用事件委派**。

---

### 練習解答 (Solution)

<details>
<summary>查看解答程式碼</summary>

```javascript
$(function() {
    // 1. 處理輸入與新增
    $("#tagInput").keydown(function(e) {
        if (e.which === 13) { // 判斷 Enter
            var val = $(this).val().trim();
            if (val !== "") {
                // 新增標籤 HTML
                $("#tagBox").append('<span class="tag">' + val + '</span>');
                // 清空輸入框
                $(this).val("");
            }
        }
    });

    // 2. 處理刪除 (事件委派)
    $("#tagBox").on("click", ".tag", function() {
        $(this).remove();
    });
});
```
</details>
