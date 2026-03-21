# 第 5 章：動畫與特效 (Effects & Animations)

## 本章目標
- 掌握 jQuery 內建的顯示、滑動與淡入淡出效果。
- 學會使用 `animate()` 建立複雜的自定義動畫。
- **理解動畫隊列 (Queue) 機制與 `stop()` 的重要性**。
- 運用回呼函式 (Callback) 確保動畫執行後的邏輯順序。
- 透過動畫提升網頁的互動品質與使用者體驗 (UX)。

---

## 5.1 內建視覺特效 (Built-in Effects)

jQuery 提供了三組最常用的視覺效果，所有方法都支援速度參數：`"slow"`, `"fast"` 或具體的毫秒數（如 `1000` 為一秒）。

### 1. 顯示與隱藏 (Display)
直接操作元素的 `display` 屬性，動畫會同時改變寬度、高度與透明度。

**情境 HTML：**
```html
<button id="showBtn">顯示</button>
<button id="hideBtn">隱藏</button>
<button id="toggleBtn">切換</button>
<div id="box" style="width:200px; height:100px; background:orange;"></div>
```

**範例操作：**
```javascript
// A. 顯示 (從左上角展開)
$("#showBtn").click(function() {
    $("#box").show(500); // 0.5 秒內顯示
});

// B. 隱藏 (縮回左上角)
$("#hideBtn").click(function() {
    $("#box").hide("slow");
});

// C. 切換 (自動判斷狀態)
$("#toggleBtn").click(function() {
    $("#box").toggle();
});
```

---

### 2. 淡入與淡出 (Fading)
僅操作元素的透明度 (`opacity`)。

**情境 HTML：**
```html
<div id="photo" style="width:200px; height:150px; background:url('cat.jpg');"></div>
```

**範例操作：**
```javascript
// A. 淡入 (從透明變不透明)
$("#photo").fadeIn(1000);

// B. 淡出 (從不透明變透明)
$("#photo").fadeOut();

// C. 設定特定透明度 (fadeTo)
// 參數：(速度, 透明度0~1)
$("#photo").fadeTo("fast", 0.5); // 變半透明
```

---

### 3. 滑動效果 (Sliding)
僅操作元素的高度 (`height`)，常用於製作「收合選單」或「FAQ 問答集」。

**情境 HTML：**
```html
<div class="question">點我查看答案</div>
<div class="answer" style="display:none; background:#eee; padding:20px;">
    這是詳細的解答內容...
</div>
```

**範例操作：**
```javascript
$(".question").click(function() {
    // 滑動顯示/隱藏
    $(".answer").slideToggle();
});
```

**執行後結果：**
- 點擊標題時，下方的內容會像捲簾一樣平滑地展開或收合。

---

### [隨堂小測驗 1]
**任務：**
1. 讓 `#alertBox` 在 2 秒內慢慢消失 (淡出)：
   ```javascript
   $("#alertBox").____(2000);
   ```
2. 點擊按鈕時，讓 `#menu` 像抽屜一樣向下滑動展開：
   ```javascript
   $("#menu").____();
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. 淡出用 fadeOut
$("#alertBox").fadeOut(2000);

// 2. 下滑展開用 slideDown
$("#menu").slideDown();
```
</details>

---

## 5.2 自定義動畫 (Custom Animations)

當內建效果不足以滿足需求時，我們可以使用 `.animate()`。

### 1. 基礎語法
`animate({ 樣式 }, 速度, 回呼函式)`

> **限制：** `animate` 只能操作**數值型**的 CSS 屬性（如：`width`, `height`, `left`, `opacity`, `fontSize`）。顏色、背景色等非數值屬性需要額外插件。

**情境 HTML：**
```html
<div id="ball" style="width:50px; height:50px; background:red; position:relative;"></div>
```

**範例操作：**
```javascript
$("#ball").click(function() {
    $(this).animate({
        left: "300px",   // 向右移動
        width: "100px",  // 變寬
        opacity: 0.5     // 變透明
    }, 1000);
});
```

### 2. 累加與累減
使用 `+=` 或 `-=` 可以讓動畫在「當前數值」基礎上進行增減。

**範例操作：**
```javascript
// 每次點擊向右移動 50px
$("#ball").animate({ left: "+=50px" });
```

---

## 5.3 動畫控制與隊列 (Queue & Stop)

這是製作互動效果時最容易遇到的地雷。

### 1. 動畫隊列 (The Queue)
如果你連續點擊按鈕 10 次，jQuery 會把這 10 次動畫「排隊」，導致你手放開後動畫還在瘋狂執行。

### 2. 停止動畫 (Stop)
`.stop()` 方法用於立即停止當前正在執行的動畫，並清除隊列。

**情境 HTML：**
```html
<nav id="navMenu">導覽列 (Hover Me)</nav>
```

**範例操作 (解決閃爍問題)：**
```javascript
$("#navMenu").hover(
    function() { 
        // 在執行新動畫前，先停止之前的，避免動畫累積
        $(this).stop().slideDown(); 
    },
    function() { 
        $(this).stop().slideUp(); 
    }
);
```

**執行後結果：**
- 當滑鼠快速進出選單時，選單會立即反應，不會出現「瘋狂上下跳動」的現象。

---

## 5.4 動畫回呼函式 (Callbacks)

如果你希望在動畫「完成之後」才執行某段程式碼（例如：動畫跑完後跳出訊息），就必須使用 Callback。

**情境 HTML：**
```html
<p id="msg">動畫執行中...</p>
```

**範例操作：**
```javascript
// 錯誤示範 (這會讓 alert 在動畫「剛開始」就跳出來)
$("#msg").hide(1000);
alert("隱藏完畢！");

// 正確示範 (確保 hide 結束後才執行)
$("#msg").hide(1000, function() {
    alert("隱藏完畢！");
});
```

---

### [隨堂小測驗 2]
**任務：**
1. 讓 `#box` 寬度變為 200px 且不透明度變為 0.2，耗時 1 秒：
   ```javascript
   $("#box").____({ width: "200px", opacity: 0.2 }, 1000);
   ```
2. 點擊按鈕時，先停止目前動畫，再進行淡入：
   ```javascript
   $("#box").____().fadeIn();
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. 自定義動畫
$("#box").animate({ width: "200px", opacity: 0.2 }, 1000);

// 2. 停止當前動畫
$("#box").stop().fadeIn();
```
</details>

---

## 5.5 綜合課後練習 (Exercise)

**專案：側邊滑動導覽欄 (Sidebar Menu)**

**任務目標：**
1. 建立一個 `index.html`，包含以下結構與樣式：
```html
<div id="sidebar" style="width:250px; height:100vh; background:#333; position:fixed; left:-250px; color:white; padding:20px;">
    <h3>Sidebar Menu</h3>
    <ul>
        <li>首頁</li>
        <li>關於我們</li>
    </ul>
</div>
<button id="toggleMenu">開啟選單</button>

<div id="overlay" style="display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.5); z-index: 5;"></div>
```

2. 撰寫 jQuery 程式碼完成以下功能：
   - **功能 A**：點擊「開啟選單」按鈕時，將 `#sidebar` 的 `left` 動畫移動到 `0px`。
   - **功能 B**：同時讓 `#overlay` (遮罩層) 淡入顯示。
   - **功能 C**：點擊 `#overlay` (遮罩層) 時，將 `#sidebar` 縮回 `left: -250px`，並讓遮罩層淡出。
   - **功能 D**：(優化) 確保所有動畫在執行前都先呼叫 `.stop()`。

---

### 練習解答 (Solution)

<details>
<summary>查看解答程式碼</summary>

```javascript
$(function() {
    // 開啟選單
    $("#toggleMenu").click(function() {
        $("#sidebar").stop().animate({ left: "0px" }, 300);
        $("#overlay").stop().fadeIn(300);
    });

    // 關閉選單 (點擊遮罩)
    $("#overlay").click(function() {
        $("#sidebar").stop().animate({ left: "-250px" }, 300);
        $(this).stop().fadeOut(300);
    });
});
```
</details>
