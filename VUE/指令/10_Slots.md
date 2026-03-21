# Chapter 10：插槽指令 - v-slot (縮寫: #)

`v-slot` 指令用於定義父組件插入子組件插槽中的內容。它取代了 Vue 2 中的 `slot` 和 `slot-scope`。

## 10.1 具名插槽 (Named Slots)

當一個組件有多個插入點時，我們使用具名插槽。縮寫語法為 `#`。

### 範例：通用卡片組件 (Card Component)

```html
<div id="app">
    <custom-card>
        <!-- 使用 v-slot:header 縮寫為 #header -->
        <template #header>
            <h3>使用者詳細資料</h3>
        </template>

        <!-- 預設插槽 (Default Slot) -->
        <p>姓名：張小明</p>
        <p>職位：資深後端工程師</p>

        <template #footer>
            <button>編輯資料</button>
        </template>
    </custom-card>
</div>

<script>
    const app = Vue.createApp({});

    // 定義一個簡單的子組件
    app.component('custom-card', {
        template: `
            <div class="card">
                <div class="header" style="background: #eee;">
                    <slot name="header">預設標題</slot>
                </div>
                <div class="body">
                    <slot>預設內容（這是 Default Slot）</slot>
                </div>
                <div class="footer">
                    <slot name="footer"></slot>
                </div>
            </div>
        `
    });

    app.mount('#app');
</script>
```

## 10.2 作用域插槽 (Scoped Slots)

作用域插槽允許子組件將資料傳遞回父組件的插槽內容中。這在渲染由組件管理的資料列表時非常強大。

### 最大運算邏輯：萬用資料列表 (Data List)

```html
<div id="app">
    <h3>產品庫存清單</h3>
    
    <product-list :items="products">
        <!-- 接收子組件傳回的資料：v-slot="slotProps" -->
        <!-- 解構賦值語法：#default="{ item }" -->
        <template #default="{ item }">
            <span :style="{ color: item.stock < 5 ? 'red' : 'green' }">
                {{ item.name }} (剩餘：{{ item.stock }})
            </span>
            <button v-if="item.stock < 5">緊急補貨</button>
        </template>
    </product-list>
</div>

<script>
    const app = Vue.createApp({
        data() {
            return {
                products: [
                    { id: 1, name: "滑鼠", stock: 10 },
                    { id: 2, name: "鍵盤", stock: 2 },
                    { id: 3, name: "螢幕", stock: 15 }
                ]
            };
        }
    });

    app.component('product-list', {
        props: ['items'],
        template: `
            <ul>
                <li v-for="item in items" :key="item.id">
                    <!-- 將 item 物件作為屬性傳遞給插槽 -->
                    <slot :item="item"></slot>
                </li>
            </ul>
        `
    });

    app.mount('#app');
</script>
```

## 10.3 為什麼要使用 v-slot？

1.  **結構與邏輯分離**：子組件負責列表迴圈、API 請求，父組件負責視覺呈現。
2.  **高度靈活性**：父組件可以隨意更換插槽內的 UI（例如將文字改為圖片），而不必修改子組件代碼。

---

## 思考練習
1. 在作用域插槽中，為什麼我們可以使用 `{ item }` 這種大括號寫法？（提示：這是 JavaScript 的解構賦值，對提升代碼簡潔度很有幫助）。
2. 在 ASP.NET Core MVC 的 Partial View（部分視圖）中，如何達到類似 Vue 插槽的效果？
    - 提示：考慮 `@RenderBody()` 或 `@await RenderSectionAsync()`。
