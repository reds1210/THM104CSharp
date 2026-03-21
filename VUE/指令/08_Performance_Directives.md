# Chapter 08：渲染效能優化 - v-once & v-memo

在大型應用或頻繁更新的資料清單中，Vue 3 提供了兩個強大的指令來控制 Virtual DOM 的更新頻率，實現「最大運算效能」優化。

## 8.1 v-once：一次性渲染

`v-once` 指令告訴 Vue 只渲染元素或組件一次。隨後的重新渲染中，元素/組件及其所有子節點將被視為靜態內容並跳過。

- **優點**：極大降低 Virtual DOM 的 Diff 成本。
- **適用場景**：資料初始化後絕不會改變的標題、導覽列、或是從 ASP.NET 控制器傳回的一次性唯讀配置。

```html
<div id="app">
    <!-- 這個區塊只會渲染一次，即便 data 改變也不會更新 -->
    <div v-once>
        <h3>固定配置：{{ systemConfig.version }}</h3>
        <p>初始化時間：{{ initTime }}</p>
    </div>

    <button @@click="initTime = '已修改'">嘗試修改（無效）</button>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                systemConfig: { version: "v1.0.2-Stable" },
                initTime: new Date().toLocaleTimeString()
            };
        }
    }).mount('#app');
</script>
```

## 8.2 v-memo (Vue 3.2+ 新增)：智慧快取

這是 Vue 3.2 引入的最強效能指令。它允許我們根據依賴項是否改變，來決定是否需要對該區塊進行更新。

### 最大運算邏輯：大數據表格行快取

假設我們有一個包含數千行的表格，每行有複雜的計算結果。如果不使用 `v-memo`，只要表格任何地方變動，Vue 就會重新檢查所有行。使用 `v-memo` 後，只有當「指定的資料」改變時，該行才會更新。

```html
<div id="app">
    <h3>效能對比監控器</h3>
    
    <!-- 僅當 item.id 或 item.updateCount 改變時，才重新渲染此 <div> -->
    <div v-for="item in bigList" :key="item.id" v-memo="[item.updateCount]">
        <p>
            ID: {{ item.id }} | 
            值: {{ item.value }} | 
            更新次數: {{ item.updateCount }}
        </p>
        <!-- 模擬複雜的嵌套邏輯 -->
        <span v-for="tag in item.tags" :key="tag">{{ tag }}</span>
    </div>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                bigList: Array.from({ length: 100 }, (_, i) => ({
                    id: i,
                    value: Math.random(),
                    updateCount: 0,
                    tags: ['A', 'B', 'C']
                }))
            };
        },
        methods: {
            // 當我們只更新某一個 item 的 updateCount 時，
            // v-memo 會確保只有那一行被更新，其他 999 行完全不參與 Diff。
            fastUpdate(index) {
                this.bigList[index].updateCount++;
            }
        }
    }).mount('#app');
</script>
```

## 8.3 何時該用哪個？

| 指令 | 原理 | 適用場景 |
| :--- | :--- | :--- |
| `v-once` | 徹底凍結，不論依賴項如何變動都不更新。 | 靜態標題、法律條款、唯讀初始化資料。 |
| `v-memo` | 條件式快取，依賴項變動時才更新。 | `v-for` 長清單中的每一行、複雜的圖表容器。 |

---

## 思考練習
1. 如果你在 `v-for` 清單中使用 `v-memo="[]"`（傳入空陣列），其效果與哪個指令相同？為什麼？
2. 在 ASP.NET Core MVC 中，我們可以使用「回應快取」（Response Caching）。這與 Vue 的 `v-memo` 在優化層級上有什麼不同？
    - 提示：考慮「網路傳輸」與「客戶端渲染」的差別。
