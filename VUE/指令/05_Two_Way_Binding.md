# Chapter 05：雙向綁定核心 - v-model 深度實戰與底層原理

`v-model` 指令在表單輸入與資料之間建立了雙向同步。本章將從最基礎的表單元素用法開始，逐步深入到其底層轉譯邏輯與高階實戰應用。

---

## 5.1 基礎表單元件綁定 (入門篇)

Vue 會根據不同的表單控制項，自動處理不同的屬性（如 `value`、`checked`）與事件。

### 範例 1：資料問卷模擬器
這是最直觀的用法，涵蓋了大部分基礎 HTML 元素。

```html
<div id="app">
    <!-- 單行文字 -->
    <p>姓名：<input type="text" v-model="profile.name"></p>
    
    <!-- 多行文字 (注意：textarea 不支援 {{ }} 插值) -->
    <p>自傳：<textarea v-model="profile.bio"></textarea></p>

    <!-- 複選框 (單個：綁定布林值) -->
    <p><input type="checkbox" v-model="profile.isMember"> 是否為會員</p>

    <!-- 單選按鈕 -->
    <p>
        性別：
        <input type="radio" value="M" v-model="profile.gender"> 男
        <input type="radio" value="F" v-model="profile.gender"> 女
    </p>

    <!-- 下拉選單 -->
    <p>
        職位：
        <select v-model="profile.job">
            <option disabled value="">請選擇</option>
            <option>前端開發</option>
            <option>後端開發</option>
        </select>
    </p>

    <pre>目前預覽資料：{{ profile }}</pre>
</div>
```

---

## 5.2 v-model 的底層原理 (原理篇)

為了理解 `v-model`，我們必須知道它本質上是 **語法糖 (Syntactic Sugar)**。

### 轉譯邏輯對比
- **文字輸入框**：轉譯為 `:value` 與 `@input`。
  ```html
  <input v-model="text"> 
  <!-- 等同於 -->
  <input :value="text" @input="text = $event.target.value">
  ```
- **複選框/單選鈕**：轉譯為 `:checked` 與 `@change`。
  ```html
  <input type="checkbox" v-model="done">
  <!-- 等同於 -->
  <input type="checkbox" :checked="done" @change="done = $event.target.checked">
  ```

---

## 5.3 複選框 (Checkbox) 的演進與進階用法

### 1. 傳統模式 vs. true-value / false-value
在傳統開發中，如果後端要求 "Y"/"N" 而非布林值，我們必須手動轉換。Vue 提供了更優雅的解決方案。

```html
<!-- 傳統手動處理 -->
<input type="checkbox" :checked="status === 'Y'" @change="status = $event.target.checked ? 'Y' : 'N'">

<!-- Vue 進階處理 (true-value / false-value) -->
<input type="checkbox" v-model="status" true-value="Y" false-value="N">
<span>目前狀態：{{ status }}</span>
```

### 2. 多選陣列 (Multiple Checkboxes)
當資料模型是 **陣列** 時，`v-model` 會自動處理「增刪」邏輯。
```html
<p>興趣：
    <input type="checkbox" value="Coding" v-model="profile.hobbies"> 編程
    <input type="checkbox" value="Music" v-model="profile.hobbies"> 音樂
</p>
<!-- profile.hobbies 將會是 ['Coding', 'Music'] -->
```

---

## 5.4 實戰場景：連動選擇器 (Cascading Select)

這是 Options API 中結合 `data`、`v-model` 與 `computed` 的「最大運算邏輯」展現。

```html
<div id="app">
    <label>縣市：</label>
    <select v-model="selectedCity">
        <option value="">請選擇</option>
        <option v-for="city in cityData" :value="city.id">{{ city.name }}</option>
    </select>

    <label>區域：</label>
    <select v-model="selectedArea" :disabled="!areas.length">
        <option value="">請選擇</option>
        <option v-for="area in areas" :value="area">{{ area }}</option>
    </select>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                selectedCity: '',
                selectedArea: '',
                cityData: [
                    { id: 'TPE', name: '台北', districts: ['大安', '信義'] },
                    { id: 'KHH', name: '高雄', districts: ['左營', '三民'] }
                ]
            };
        },
        computed: {
            areas() {
                const city = this.cityData.find(c => c.id === this.selectedCity);
                return city ? city.districts : [];
            }
        }
    }).mount('#app');
</script>
```

---

## 5.5 內建修飾符 (v-model Modifiers)

### 範例：資產輸入驗證器
```html
<div id="app">
    <!-- .lazy：取代 input 事件，改在 change (失去焦點/Enter) 時同步 -->
    <input v-model.lazy="username" placeholder="離開焦點才同步">

    <!-- .number：自動轉型為數字，對後端 C# 模型綁定至 int/decimal 非常關鍵 -->
    <input v-model.number="balance" type="text" placeholder="輸入金額">

    <!-- .trim：自動過濾前後空白字元 -->
    <input v-model.trim="email" placeholder="輸入 Email">
</div>
```

---

## 5.6 高階技巧：Computed Setter (攔截寫入)

當你需要在資料寫回 `data` 之前進行邏輯處理（例如強制大寫）時使用。

```javascript
computed: {
    upperName: {
        get() { return this.rawName; },
        set(val) { this.rawName = val.toUpperCase().trim(); }
    }
}
// HTML: <input v-model="upperName">
```

---

## 5.7 重要注意事項：IME 輸入法 (中文/日文)

**IME 行為**：`v-model` 在處理中文輸入法時，**不會** 在拼音/注音階段更新資料，只有在選定字詞後才會觸發。
- **解決方案**：如果你需要「鍵入即反應」的即時搜尋，應改用 `:value` + `@input` 的手動模式。

---

## 思考練習
1. 為什麼在多選 Checkbox 中，`v-model` 必須綁定到一個 `Array`？
2. 如果你的後端 C# 模型中 `Age` 是 `int` 型別，前端漏加了 `.number` 會發生什麼事？
3. `v-model.lazy` 在處理超過 100 個欄位的巨型表單時，如何提升網頁效能？
