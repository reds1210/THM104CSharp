# Chapter 03：動態屬性控制 - v-bind 深度運用

`v-bind` 指令（縮寫為 `:`）用於動態地綁定一個或多個 HTML 屬性，或一個組件的 prop 到表達式。

## 3.1 基礎屬性綁定

在 Razor 視圖中，我們經常需要將伺服器端資料與 Vue 的屬性同步。

```html
<div id="app">
    <!-- 縮寫語法 : -->
    <a :href="url" :title="linkTitle">
        <img :src="imgSrc" :alt="altText" style="width: 100px;">
    </a>
    
    <!-- 綁定布林屬性：disabled, checked, selected -->
    <button :disabled="isProcessing">
        {{ isProcessing ? "處理中..." : "提交申請" }}
    </button>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                url: "https://google.com",
                linkTitle: "點擊跳轉",
                imgSrc: "logo.png",
                altText: "公司標誌",
                isProcessing: true
            };
        }
    }).mount('#app');
</script>
```

## 3.2 Class 與 Style 的極致運用

這是 `v-bind` 最強大的地方：它支持物件與陣列語法，允許我們根據多個邏輯條件來決定樣式。

### 最大運算邏輯：狀態顯示器

我們將根據一個複雜的數值模型，動態計算物件的 CSS Class。

```html
<div id="app">
    <!-- 物件語法：{ 'className': 布林值 } -->
    <div class="status-box" 
         :class="{ 'active': isActive, 'warning': currentLoad > 80, 'danger': currentLoad > 95 }">
        系統當前負載：{{ currentLoad }}%
    </div>

    <!-- 陣列語法：結合多個樣式邏輯 -->
    <div :style="[baseStyles, themeStyles]">
        內聯樣式組合
    </div>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                isActive: true,
                currentLoad: 88,
                baseStyles: {
                    fontSize: '16px',
                    padding: '10px'
                }
            };
        },
        computed: {
            // 最大運算邏輯：抽離複雜的樣式判斷
            themeStyles() {
                return {
                    backgroundColor: this.currentLoad > 90 ? '#ffcccc' : '#e2e2e2',
                    color: this.currentLoad > 90 ? 'red' : '#333',
                    transition: 'all 0.5s ease'
                };
            }
        }
    }).mount('#app');
</script>
```

## 3.3 動態參數與多屬性綁定

Vue 3 允許我們動態定義屬性名稱，並一次性綁定多個屬性。

```html
<div id="app">
    <!-- 動態參數：v-bind:[attributeName] -->
    <button v-bind:[attrName]="attrValue">動態屬性按鈕</button>

    <!-- 一次綁定多個屬性 (Object of Attributes) -->
    <input v-bind="inputProps">
</div>

<script>
    Vue.createApp({
        data() {
            return {
                attrName: "title",
                attrValue: "這是一個動態標題",
                inputProps: {
                    type: "text",
                    placeholder: "請輸入帳號",
                    required: true,
                    maxlength: 10
                }
            };
        }
    }).mount('#app');
</script>
```

---

## 思考練習
1. 如果一個標籤同時擁有靜態的 `class="box"` 和動態的 `:class="{ active: true }"`，最終渲染出來的結果是什麼？Vue 是如何合併它們的？
2. 在處理大型清單時，直接在 HTML 內寫複雜的 `:style` 運算（如三元運算子）好，還是寫在 `computed` 中好？為什麼？
