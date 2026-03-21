# Chapter 02：單向資料綁定 - 文字與 HTML

在本章中，我們將探討 Vue 3 如何將資料單向渲染到 DOM 中。這包含基本的文字替換、HTML 渲染，以及跳過編譯階段以提升效能的技巧。

## 2.1 Mustache {{ }} 與 v-text

這兩者都用於純文字渲染。
- **Mustache `{{ }}`**：最常用，可以在 HTML 標籤內靈活組合文字。
- **`v-text` 指令**：會取代整個標籤的 `textContent`，通常用於防止初次載入時出現未編譯的大括號。

### 範例：日誌分析系統（文字運算）

```html
<div id="app">
    <!-- Mustache 語法：適合拼接文字 -->
    <h3>系統狀態：{{ statusMessage + " (最後更新: " + updateTime + ")" }}</h3>

    <!-- v-text 指令：取代整個內容 -->
    <p v-text="logDetails"></p>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                statusMessage: "運作中",
                updateTime: new Date().toLocaleTimeString(),
                logDetails: "初始化完成，負載穩定。"
            };
        }
    }).mount('#app');
</script>
```

## 2.2 v-html：動態渲染與安全性 (XSS)

`v-html` 用於將包含 HTML 標籤的字串渲染為真正的 DOM 元素。

> **⚠️ 安全警告**：絕對不要將使用者提供的內容（如留言、評論）直接放入 `v-html`，這會導致跨站腳本攻擊 (XSS)。

### 最大運算邏輯：高階渲染器（HTML 格式化）

在此範例中，我們模擬後端傳回的格式化代碼，並根據嚴重等級動態生成帶有樣式的 HTML。

```html
<div id="app">
    <h4>伺服器事件日誌：</h4>
    <!-- 僅用於受信任的後端 HTML 資料 -->
    <div class="log-container" v-html="renderLogWithStyle(rawLog)"></div>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                rawLog: "DB_CONNECTION_LOST|CRITICAL|2026-03-05"
            };
        },
        methods: {
            renderLogWithStyle(data) {
                // 最大運算邏輯：解析複雜字串並封裝為 HTML
                const [code, level, time] = data.split('|');
                let color = level === 'CRITICAL' ? 'red' : 'blue';
                
                // 模擬高階邏輯處理與 DOM 字串封裝
                return `
                    <div style="border-left: 5px solid ${color}; padding: 10px; background: #f9f9f9;">
                        <strong style="color: ${color}">[${level}]</strong>
                        <span style="margin-left: 10px;">${code}</span>
                        <br/>
                        <small>時間戳記：${time}</small>
                    </div>
                `;
            }
        }
    }).mount('#app');
</script>
```

## 2.3 v-pre：效能優化

`v-pre` 是一個特殊的指令，它告訴 Vue **不要編譯** 這個標籤及其子標籤。
- **何時使用**：當標籤內包含大量的靜態內容，或者包含與 Vue 語法相似但不需要運算的文字（如數學公式或代碼範例）時。
- **優勢**：跳過 Virtual DOM 的 Diff 過程，對於大型、靜態的 UI 區塊能顯著提升效能。

```html
<!-- Vue 將會跳過此區塊的編譯，節省運算成本 -->
<div v-pre>
    這是一個純靜態區塊，
    {{ 這裏的大括號不會被 Vue 處理 }}
    即使這裡有 v-if 或 v-for 也會被視為普通屬性。
</div>
```

---

## 思考練習
1. 如果你在 `v-html` 中渲染了一個包含 `v-on:click` 的按鈕，Vue 會監聽到這個事件嗎？（提示：考慮指令的編譯時機）。
2. 在 ASP.NET Core MVC 中，如果你使用 `Html.Raw()` 渲染一段內容，與 Vue 的 `v-html` 有什麼本質上的差別？
