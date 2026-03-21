# 第 6 章：DOM 遍歷 (Traversing)

## 本章目標
- 深刻理解 DOM 樹狀結構，學會利用「親屬關係」精準選取元素。
- 掌握向上、向下、同層查找的完整方法組。
- **分辨相似方法的差異 (如 `parent` vs `closest`, `find` vs `children`)**。
- 學會使用 `.end()` 與 `.addBack()` 控制遍歷狀態，實現複雜的鏈式操作。
- 透過遍歷技巧撰寫具備「結構適應性」的代碼，降低與 HTML 的耦合度。

---

## 6.1 向上遍歷：祖先查找 (Ancestors)

### 1. 基礎祖先查找
- **`parent()`**: 僅向上查找**一層**。
- **`parents(selector)`**: 向上查找**所有**祖先，直到 `<html>`。
- **`parentsUntil(selector)`**: 向上查找，直到遇到目標為止（不含目標）。

### 2. `closest(selector)` - 最近符合者 (實戰首選)
從自己開始往上找，直到找到第一個符合條件的祖先。這在處理「組件化」的 HTML 結構時非常穩定。

### 3. `offsetParent()` - 定位祖先 (進階)
尋找最近一個具有 `position: relative/absolute/fixed` 的父元素。
> **用途**：當你需要計算元素的絕對座標或處理複雜排版時。

**範例操作：**
```javascript
// 尋找按鈕所在的定位上下文
var $container = $("#btn").offsetParent(); 
console.log("定位參考對象是：" + $container.prop("tagName"));
```

---

## 6.2 向下遍歷：後代查找 (Descendants)

### 1. `children(selector)` - 直系小孩
僅搜尋下一層的子元素。

### 2. `find(selector)` - 所有後代 (效能之王)
在所有層級中搜尋。這是 jQuery 搜尋速度最快的方法。

### 3. `contents()` - 內容遍歷 (特殊)
包含文字節點與註解。
> **用途**：抓取元素內的「純文字節點」（非標籤包圍的文字）或進入 `iframe` 內部操作。

---

## 6.3 同層遍歷：兄弟查找 (Siblings)

### 1. 基礎兄弟查找
- **`siblings()`**: 除了自己以外的所有兄弟。
- **`next()` / `prev()`**: 緊鄰的下一個/上一個。

### 2. 範圍兄弟查找
- **`nextAll()` / `prevAll()`**: 後面/前面所有的兄弟。
- **`nextUntil(selector)`**: 範圍選取。

---

## 6.4 條件過濾與精煉 (Filtering)

### 1. 基本過濾：`first()`, `last()`, `eq(index)`

### 2. 範圍過濾：`slice(start, [end])`
像陣列一樣切片。
```javascript
// 選取第 2 個到第 4 個 li (不含第 5 個)
$("li").slice(1, 4).css("color", "blue");
```

### 3. 進階過濾：`filter(function)`
當選擇器無法表達複雜邏輯時，傳入一個函式。
```javascript
$("div").filter(function() {
    // 僅留下寬度大於 500px 的 div
    return $(this).width() > 500;
}).addClass("wide-box");
```

---

## 6.5 鏈式操作的高級控制 (Chaining Control)

這是將多行程式碼濃縮成一行且保持邏輯清晰的關鍵。

### 1. `.add(selector)`
將新的元素併入目前的選取集合。

### 2. `.addBack()` - 找回起點 (重要！)
在進行遍歷操作後，將「發起遍歷的原始元素」加回集合中。

**情境 HTML：**
```html
<ul class="menu">
    <li class="active">首頁 (目前)</li>
    <li>產品</li>
    <li>關於</li>
</ul>
```

**範例操作：**
```javascript
// 需求：讓目前的項及其後面所有項都變灰
$(".active").nextAll().addBack().css("opacity", 0.5);
// 如果不加 addBack()，變灰的只有「產品」和「關於」，「首頁」不會變。
```

### 3. `.end()` - 回到過去
回到上一次遍歷操作前的狀態。

**範例操作：**
```javascript
$(".container")
    .find(".header").css("color", "red").end() // 處理完 header 後回到 container
    .find(".footer").css("color", "blue");     // 重新從 container 找 footer
```

---

## 6.6 遍歷方法對照表 (Cheat Sheet)

| 需求 | 方法 | 備註 |
| :--- | :--- | :--- |
| **向上找最近容器** | `closest()` | 實戰最常用 |
| **向下挖特定目標** | `find()` | 效能最優 |
| **找鄰居** | `next()` / `prev()` | 常用於選單切換 |
| **排除特定元素** | `not()` | |
| **檢查狀態** | `is()` | 回傳 true/false |
| **包含自己** | `addBack()` | 鏈式操作必備 |

---

### [隨堂小測驗]
**任務：**
1. 如何選取所有 `div` 中，高度小於 100px 的元素？
   ```javascript
   $("div").____(function() { return $(this).height() < 100; });
   ```
2. 執行 `$(".item").nextAll().____().hide()` 可以讓 `.item` 本身及其後面的兄弟全部隱藏。

<details>
<summary>查看解答</summary>

1. **filter**
2. **addBack**
</details>

---

## 6.7 綜合課後練習 (Exercise)

**專案：多級聯動與狀態追蹤 (Multi-level Interaction)**

**任務目標：**
1. 建立一個 `index.html`：
```html
<div class="product-card">
    <h2 class="title">高級耳機</h2>
    <div class="description">
        <p>這是一款非常棒的耳機...</p>
        <button class="expand-btn">顯示更多</button>
    </div>
    <div class="spec" style="display:none;">
        <ul>
            <li>藍牙 5.0</li>
            <li>降噪功能</li>
        </ul>
    </div>
</div>
```

2. 撰寫 jQuery 程式碼完成以下功能：
   - **功能 A：局部切換**。點擊 `.expand-btn` 時，找到該按鈕所屬 `.product-card` 內的 `.spec` 並將其滑動切換 (`slideToggle`)。
   - **功能 B：標題聯動**。當 `.spec` 被展開時，將對應的 `.title` 變為紅色；收合時變回黑色。
   - **功能 C：鏈式練習**。點擊標題時，使用 `.nextAll()` 搭配 `.addBack()` 將該標題下的所有內容區塊透明度變為 0.5。

---

### 練習解答 (Solution)

<details>
<summary>查看解答程式碼</summary>

```javascript
$(function() {
    // 功能 A & B
    $(".expand-btn").click(function() {
        var $card = $(this).closest(".product-card");
        var $spec = $card.find(".spec");

        $spec.slideToggle(function() {
            // 在動畫完成後判斷狀態
            if ($(this).is(":visible")) {
                $card.find(".title").css("color", "red");
            } else {
                $card.find(".title").css("color", "black");
            }
        });
    });

    // 功能 C: 鏈式遍歷
    $(".title").click(function() {
        // 選取自己和後面所有兄弟，變半透明
        $(this).nextAll().addBack().css("opacity", 0.5);
    });
});
```
</details>
