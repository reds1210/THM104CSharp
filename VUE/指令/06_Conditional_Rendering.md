# Chapter 06：條件渲染的決策 - v-if 與 v-show

在 Vue 3 中，控制元素的顯示與隱藏有兩種主要方式。雖然它們在視覺效果上相似，但底層邏輯與效能開銷卻大相徑庭。

## 6.1 v-if 鏈式邏輯

`v-if` 是「真正的」條件渲染，因為它會確保在切換過程中條件塊內的事件監聽器和子組件適當地被銷毀和重建。

### 最大運算邏輯：API 狀態切換器

這是一個模擬 ASP.NET MVC 後端非同步請求後的狀態處理：

```html
<div id="app">
    <h3>系統連線狀態</h3>
    <button @@click="simulateFetch">刷新狀態</button>

    <!-- v-if / v-else-if / v-else 必須緊鄰 -->
    <div v-if="status === 'loading'" class="loading-box">
        正在請求資料運算中...
    </div>

    <div v-else-if="status === 'success'" class="success-box">
        資料讀取成功！內容：{{ payload }}
    </div>

    <div v-else-if="status === 'error'" class="error-box">
        連線失敗，請檢查網路設定。
    </div>

    <div v-else>
        初始狀態。
    </div>

    <!-- 在 <template> 上使用 v-if：同時切換多個標籤，但 template 不會出現在 DOM 中 -->
    <template v-if="isAdmin">
        <hr>
        <h4>管理員專區</h4>
        <button>清空資料庫</button>
    </template>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                status: "idle", // idle, loading, success, error
                payload: null,
                isAdmin: true
            };
        },
        methods: {
            simulateFetch() {
                this.status = "loading";
                // 模擬非同步運算
                setTimeout(() => {
                    this.status = Math.random() > 0.3 ? "success" : "error";
                    this.payload = { id: 101, value: "Node_Data_X" };
                }, 1000);
            }
        }
    }).mount('#app');
</script>
```

## 6.2 v-if vs v-show：效能與決策

- **`v-if` (條件渲染)**：
    - **原理**：元素會被「物理性」地從 DOM 中移除或插入。
    - **缺點**：切換開銷較高。
    - **優點**：初次渲染開銷較低（若條件為假）。
    - **適用場景**：切換頻率低、條件複雜、涉及權限安全性。

- **`v-show` (顯示切換)**：
    - **原理**：元素始終保留在 DOM 中，僅透過 CSS `display: none;` 切換。
    - **缺點**：初次渲染開銷較高（無論真假都會載入 DOM）。
    - **優點**：切換開銷極低。
    - **適用場景**：切換極度頻繁、選單顯示、工具列彈窗。

```html
<div id="app">
    <!-- 切換頻率高的場景使用 v-show -->
    <button @@mouseover="showTip = true" @@mouseleave="showTip = false">
        懸停顯示說明
    </button>
    
    <p v-show="showTip" style="background: yellow;">
        這是一個頻繁切換的提示框。
    </p>
</div>

<script>
    Vue.createApp({
        data() {
            return { showTip: false };
        }
    }).mount('#app');
</script>
```

---

## 思考練習
1. 如果一個父元素有 `v-if="false"`，而它的子元素有 `v-show="true"`，子元素會顯示出來嗎？為什麼？
2. 在 ASP.NET Core MVC 的伺服器端渲染中，我們通常使用 `@if (Model.IsActive) { ... }`。這與 Vue 的 `v-if` 有什麼決定性的差異？
    - 提示：考慮對 HTML 源代碼（View Source）的影響。
