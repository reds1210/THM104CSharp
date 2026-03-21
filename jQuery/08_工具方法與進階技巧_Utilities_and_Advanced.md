# 第 8 章：工具方法與進階技巧 (Utilities & Advanced)

## 本章目標
- 掌握 jQuery 提供的強大工具函式，處理陣列與物件。
- 理解並運用「鏈式語法 (Chaining)」提升程式碼簡潔度。
- 學會解決與其他套件衝突的 `$.noConflict()` 方法。
- **掌握 jQuery 插件 (Plugin) 的開發基礎，擴充自定義功能**。
- 瞭解 jQuery 在現代前端開發中的定位與優化建議。

---

## 8.1 集合與物件處理 (Collections & Objects)

jQuery 不僅能操作 DOM，還提供了一系列處理 JavaScript 基本型別（陣列、物件）的靜態工具方法（以 `$.` 開頭）。

### 1. 通用迴圈 ($.each)
這是 `$(selector).each()` 的通用版本，可以用於遍歷任何陣列或物件。

**範例操作：**
```javascript
// A. 遍歷陣列
var colors = ["Red", "Green", "Blue"];
$.each(colors, function(index, value) {
    console.log("第 " + index + " 個顏色是：" + value);
});

// B. 遍歷物件 (Key-Value)
var user = { name: "John", age: 25, job: "Designer" };
$.each(user, function(key, val) {
    console.log(key + ": " + val);
});
```

---

### 2. 陣列轉換 ($.map)
當你想要將一個陣列轉換成另一種形式時（例如：從物件陣列中只提取 ID），`$.map()` 是最佳選擇。

**範例操作：**
```javascript
var students = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" }
];

// 提取所有學生的姓名，組成新陣列
var names = $.map(students, function(item) {
    return item.name;
});
// 結果: ["Alice", "Bob"]
```

---

### 3. 物件合併 ($.extend)
常用於合併「預設參數」與「使用者設定」。

**範例操作：**
```javascript
var defaults = { theme: "light", showSidebar: true };
var options = { theme: "dark" };

// 將 options 合併到 defaults 中
var settings = $.extend({}, defaults, options);
// 結果: { theme: "dark", showSidebar: true }
```

---

### [隨堂小測驗 1]
**任務：**
1. 使用 `$.each` 遍歷陣列 `[10, 20, 30]` 並加總：
   ```javascript
   var total = 0;
   $.____([10, 20, 30], function(i, v) { total += v; });
   ```
2. 檢查陣列中是否存在某個值（若不存在回傳 -1）：
   ```javascript
   var idx = $.____("Apple", ["Banana", "Apple", "Orange"]); // 應回傳 1
   ```

<details>
<summary>查看解答</summary>

```javascript
// 1. $.each
$.each([10, 20, 30], function(i, v) { total += v; });

// 2. $.inArray
var idx = $.inArray("Apple", ["Banana", "Apple", "Orange"]);
```
</details>

---

## 8.2 進階技巧與優化 (Advanced & Optimization)

### 1. 衝突處理 ($.noConflict)
如果頁面同時引入了其他也使用 `$` 作為變數名的庫（如 Prototype.js），可以使用此方法釋放 `$` 的控制權。

**範例操作：**
```javascript
// 釋放 $ 的控制權
$.noConflict();

// 之後必須使用全名 jQuery
jQuery("#box").hide();

// 或者使用閉包重新定義區域性的 $
(function($) {
    $("#box").show(); // 這裡面的 $ 依然指向 jQuery
})(jQuery);
```

---

### 2. 效能優化建議
- **快取 (Caching)**：不要在迴圈裡重複選取同一個元素。
  ```javascript
  // 錯誤寫法
  for(var i=0; i<100; i++) { $("#list").append(i); }
  
  // 優化寫法：先存進變數 (通常以 $ 開頭命名)
  var $list = $("#list");
  for(var i=0; i<100; i++) { $list.append(i); }
  ```
- **鏈式語法 (Chaining)**：減少重複選取，提升效率。
  ```javascript
  $("#box").css("color", "red").slideDown(500).addClass("active");
  ```

---

## 8.3 擴充 jQuery (Developing Plugins)

開發插件可以讓你將重複的邏輯封裝起來，讓團隊成員輕鬆複用。透過擴充 `$.fn` 物件，你可以替 jQuery 加入自定義的新方法。

### 1. 插件開發核心觀念 (The Basics)
在開發插件時，有三個最重要的規則：
1. **`$.fn` 是入口**：所有插件方法都必須掛載在 `$.fn` 下。
2. **`this` 的身分**：在插件函式內部，`this` 指向的是**「目前的 jQuery 物件集合」**，因此可以直接呼叫 `.css()`, `.attr()` 等 jQuery 方法。
3. **鏈式語法 (Chaining)**：必須在函式最後 `return this`，讓後續能接續呼叫其他 jQuery 方法。

**範例操作：基礎高亮插件**
```javascript
$.fn.highlight = function(color) {
    // 這裡的 this 就是 jQuery 物件
    this.css("background-color", color || "yellow");
    
    // 必須回傳 this，否則後面的 .fadeOut() 會報錯
    return this; 
};

// 使用：
$("p").highlight("pink").fadeOut(2000);
```

---

### 2. 處理多個元素 (Iteration)
當使用者選取多個元素（例如 `$("li").myPlugin()`）時，你的插件代碼必須確保每個元素都被正確處理。這時需要使用 `this.each()`。

**情境 HTML：**
```html
<p>段落 1</p>
<p>段落 2</p>
```

**範例操作：正確的遍歷寫法**
```javascript
$.fn.showTag = function() {
    // 使用 each 確保每個選中的元素都跑一次邏輯
    return this.each(function() {
        // 在 each 裡面，this 會變成「原生 DOM 元素」
        // 所以要用 $(this) 包起來才能用 jQuery 方法
        var tagName = $(this).prop("tagName");
        $(this).append(" <b>(標籤名: " + tagName + ")</b>");
    });
};

// 使用：
$("p").showTag();
```

---

### 3. 支援參數與預設值 (Options & Defaults)
高品質的插件應該允許使用者自定義設定，同時具備「預設值」。我們通常使用 `$.extend()` 來處理這部分。

**範例操作：自定義陰影插件**
```javascript
$.fn.addShadow = function(options) {
    // 1. 設定預設值
    var defaults = {
        color: "rgba(0,0,0,0.5)",
        blur: "10px",
        spread: "5px"
    };

    // 2. 將使用者傳入的 options 合併到預設值中
    var settings = $.extend({}, defaults, options);

    return this.each(function() {
        $(this).css("box-shadow", "0 0 " + settings.blur + " " + settings.spread + " " + settings.color);
    });
};

// 使用 A：使用預設值
$(".box").addShadow();

// 使用 B：自定義顏色
$(".card").addShadow({ color: "red", blur: "20px" });
```

---

### 4. 實戰範例：密碼顯示/隱藏切換器
這是一個非常實用的功能，將其封裝成插件後，全站的密碼欄位都能一鍵套用。

**情境 HTML：**
```html
<input type="password" class="pw-input">
<input type="password" class="pw-input">
```

**範例操作：**
```javascript
$.fn.togglePassword = function() {
    return this.each(function() {
        var $input = $(this);
        
        // 在輸入框後方動態加入一個按鈕
        var $btn = $('<button type="button">顯示</button>');
        $input.after($btn);

        // 點擊事件邏輯
        $btn.on("click", function() {
            if ($input.attr("type") === "password") {
                $input.attr("type", "text");
                $btn.text("隱藏");
            } else {
                $input.attr("type", "password");
                $btn.text("顯示");
            }
        });
    });
};

// 使用：一鍵套用
$(".pw-input").togglePassword();
```

---

### 5. 進階保護：使用 IIFE 封裝
為了防止插件內部的變數汙染全域，或是防止 `$` 符號被其他套件搶走，標準的插件寫法會包裹在一個立即執行函式 (IIFE) 中。

**推薦的插件樣板：**
```javascript
(function($) {
    // 這裡面的 $ 絕對會指向 jQuery，非常安全
    $.fn.myPlugin = function() {
        return this.each(function() {
            // 插件邏輯...
        });
    };
})(jQuery);
```

---

### [隨堂小測驗 3]
**任務：**
1. 在插件內部，如何將「原生 DOM」轉為「jQuery 物件」以使用 `.css()` 方法？
2. 為什麼插件最後一定要執行 `return this`？

<details>
<summary>查看解答</summary>

1. 使用 **`$(this)`**。
2. 為了支援 **鏈式語法 (Chaining)**，讓使用者可以接著呼叫下一個方法（如 `.hide()`）。
</details>

---

## 8.4 課程總結與未來 (Summary)

### 1. jQuery 的現狀
雖然現代前端（Vue, React）興起，但 jQuery 在以下情境依然不可替代：
- **老舊專案維護**：大量現有的企業網頁仍在使用。
- **簡單網頁、活動頁**：不需要複雜框架，快速達成動畫與 AJAX。
- **強大的生態系**：無數成熟的插件（DataTable, Select2）依賴 jQuery。

### 2. 學習建議
- 理解 jQuery 的底層是 DOM 操作，這對學習任何框架都有幫助。
- 在大型專案中，建議優先考慮 **資料驅動 (Data-driven)** 的框架（如 Vue），但在操作 DOM 特效時，jQuery 依然是你的好幫手。

---

## 8.5 綜合課後練習 (Exercise)

**專案：自定義「背景切換與閃爍」插件**

**任務目標：**
1. 建立一個 `index.html`，包含三個 `div` 區塊。
2. 撰寫一個 jQuery Plugin `$.fn.flashColor`。
3. **插件功能**：
   - 接收兩個參數：顏色 (`color`) 與 閃爍次數 (`times`)。
   - 點擊元素時，該元素背景會變成指定顏色，並快速淡入淡出指定次數。
   - **必須支援鏈式語法**。

---

### 練習解答 (Solution)

<details>
<summary>查看解答程式碼</summary>

```javascript
// 1. 插件定義
$.fn.flashColor = function(color, times) {
    var $el = this; // 快取目前的 jQuery 物件
    
    // 綁定點擊事件
    $el.click(function() {
        $(this).css("background-color", color);
        
        // 簡單的閃爍循環
        for (var i = 0; i < times; i++) {
            $(this).fadeOut(100).fadeIn(100);
        }
    });

    return this; // 支援 Chaining
};

// 2. 插件呼叫
$(function() {
    $(".box").flashColor("red", 3).css("border", "5px solid black");
});
```
</details>
