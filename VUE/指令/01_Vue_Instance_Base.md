# Chapter 01：Vue 3 實體與生命週期基礎

在本章節中，我們將學習如何在不使用建構工具（Vite/Webpack）的情況下，透過 CDN 將 Vue 3 整合進 ASP.NET Core MVC 的 `.cshtml` 視圖中。

## 1.1 環境配置 (CDN)

在 Razor 視圖中，我們通常在 `Scripts` 區塊引入 Vue 3：

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

## 1.2 Options API 基本結構

Vue 3 的 Options API 讓邏輯清晰地組織在不同的屬性中。

### 範例：系統資源監控模擬器

此範例展示了 `data` 與 `methods` 的連動，並模擬一個高頻率更新的邏輯運算。

```html
<div id="app">
    <div class="card">
        <h3>伺服器狀態：{{ serverName }}</h3>
        <p>當前負載：{{ cpuLoad }}%</p>
        <button v-on:click="optimizeSystem">執行系統優化運算</button>
    </div>
</div>

<script>
    const { createApp } = Vue;

    createApp({
        // 資料模型：定義響應式狀態
        data() {
            return {
                serverName: "Production_Node_01",
                cpuLoad: 85,
                threshold: 90
            };
        },
        // 方法：定義業務邏輯與運算
        methods: {
            optimizeSystem() {
                // 最大運算邏輯：模擬資源分配算法
                console.log("開始計算最佳化路徑...");
                const reduction = Math.floor(Math.random() * 15) + 5;
                this.cpuLoad = Math.max(0, this.cpuLoad - reduction);
                
                if (this.cpuLoad < 50) {
                    this.serverName += " (Optimized)";
                }
            }
        },
        // 生命週期鉤子：掛載完成後執行的邏輯
        mounted() {
            console.log("Vue 實體已掛載，監控開始...");
        }
    }).mount('#app');
</script>
```

## 1.3 Razor 衝突處理 (關鍵！)

在 `.cshtml` 中使用 Vue 指令時，必須注意以下衝突：

1.  **@ 符號衝突**：
    *   Razor 使用 `@` 作為語法開頭。
    *   Vue 的 `v-on:` 縮寫也是 `@`。
    *   **解決方案**：使用 `@@` 轉義（例如 `@@click="..."`）或堅持使用完整指令 `v-on:click`。

2.  **大括號衝突**：
    *   雖然 Razor 不常使用 `{{}}`，但在某些極端配置下可能衝突。
    *   **解決方案**：在掛載時修改 `delimiters`（不建議，除非必要）。

---

## 思考練習
為什麼在生產環境中，我們更傾向於使用 `v-on:click` 而不是縮寫 `@@click`？這與程式碼的可讀性與 Razor 編譯器的負擔有何關係？
