# Chapter 07：高效列表渲染 - v-for

`v-for` 指令基於一個陣列或物件來渲染一個列表。在處理 ASP.NET Core MVC 的伺服器端資料集合（如 `List<T>`）時，這是最核心的 Vue 指令。

## 7.1 基本迭代與 key 的關鍵性

在使用 `v-for` 時，**必須** 同時綁定一個唯一的 `:key`。
- **為什麼需要 key？**：Vue 的 Virtual DOM 使用 key 來識別各個節點，這對於「就地更新」策略至關重要，能大幅提升渲染效能並避免組件狀態錯亂。

### 最大運算邏輯：員工清單渲染器

```html
<div id="app">
    <h3>公司通訊錄</h3>
    
    <!-- 基本陣列迭代 (item, index) -->
    <ul>
        <li v-for="(emp, idx) in employees" :key="emp.id">
            [{{ idx + 1 }}] {{ emp.name }} - 職位: {{ emp.role }}
        </li>
    </ul>

    <!-- 物件屬性迭代 (value, key, index) -->
    <h4>伺服器配置詳情：</h4>
    <div v-for="(val, key) in serverConfig" :key="key">
        <strong>{{ key }}</strong>: {{ val }}
    </div>

    <!-- 數字範圍迭代 -->
    <span v-for="n in 5" :key="n" style="margin-right: 5px;">頁碼 {{ n }}</span>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                employees: [
                    { id: 101, name: "Alice", role: "Dev" },
                    { id: 102, name: "Bob", role: "Design" },
                    { id: 103, name: "Charlie", role: "PM" }
                ],
                serverConfig: {
                    Host: "Production_1",
                    IP: "192.168.1.1",
                    Status: "Online"
                }
            };
        }
    }).mount('#app');
</script>
```

## 7.2 v-if 與 v-for 的優先級戰爭 (Vue 3 改動)

> **⚠️ 重要更動**：在 Vue 2 中 `v-for` 優先級較高，但在 **Vue 3** 中 `v-if` 的優先級比 `v-for` 更高。

這意味著 `v-if` 無法存取 `v-for` 迴圈中的變數。如果你嘗試在同一個標籤上同時使用它們，會報錯。

### 解決方案：最大運算邏輯（計算過濾）

**錯誤寫法（不要這樣做）：**
`<li v-for="user in users" v-if="user.isActive">...</li>` (在 Vue 3 中會報錯，因為 `user` 在 `v-if` 中還不存在)。

**正確寫法：使用計算屬性 (Computed)**

```html
<div id="app">
    <h3>已啟用用戶：</h3>
    <!-- 1. 效能優化：僅渲染過濾後的清單 -->
    <ul>
        <li v-for="user in activeUsers" :key="user.id">
            {{ user.name }} (最後登入: {{ user.lastSeen }})
        </li>
    </ul>
    
    <!-- 2. 若要進行邏輯包裝，請使用 <template> -->
    <template v-for="item in items" :key="item.id">
        <div v-if="item.show" class="item-card">
            {{ item.text }}
        </div>
    </template>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                users: [
                    { id: 1, name: "User_A", isActive: true, lastSeen: "2026-03-01" },
                    { id: 2, name: "User_B", isActive: false, lastSeen: "2026-02-15" },
                    { id: 3, name: "User_C", isActive: true, lastSeen: "2026-03-05" }
                ],
                items: [
                    { id: 10, text: "內容A", show: true },
                    { id: 11, text: "內容B", show: false }
                ]
            };
        },
        computed: {
            // 最大運算邏輯：將邏輯從 HTML 模板抽離到 JavaScript
            activeUsers() {
                return this.users.filter(u => u.isActive);
            }
        }
    }).mount('#app');
</script>
```

---

## 思考練習
1. 為什麼在 `v-for` 中使用陣列的 `index` 作為 `:key` 通常不是一個好主意？（提示：當陣列順序發生變動時）。
2. 在 ASP.NET Core Razor 視圖中，我們常用 `@foreach`。為什麼在 Vue 中，我們更傾向於在後端先傳回 JSON，再使用 `v-for` 來渲染，而不是直接用 Razor 生成 HTML 列表？
    - 提示：考慮客戶端的排序與過濾效能。
