# Chapter 04：事件監聽器 - v-on (縮寫: @)

`v-on` 指令用於監聽 DOM 事件，並在事件觸發時執行 JavaScript。在 ASP.NET Core MVC 中，我們經常使用它來處理點擊、提交或鍵盤輸入。

## 4.1 基礎用法與縮寫

在 Razor 視圖中，記住縮寫 `@` 需要轉義為 `@@`。

```html
<div id="app">
    <!-- 1. 內聯處理器：簡單邏輯 -->
    <button v-on:click="count++">加 1：{{ count }}</button>
    
    <!-- 2. 方法處理器：處理複雜運算 -->
    <!-- 縮寫：@@click (Razor 轉義後變成 @click) -->
    <button @@click="handleClick">點擊警告</button>

    <!-- 3. 傳遞參數 -->
    <button @@click="sayHello('User_ID: 101', $event)">顯示資訊</button>
</div>

<script>
    Vue.createApp({
        data() { return { count: 0 }; },
        methods: {
            handleClick() { alert("按鈕已點擊！"); },
            sayHello(msg, event) {
                console.log(msg, event); // $event 是原生 DOM 事件對象
            }
        }
    }).mount('#app');
</script>
```

## 4.2 事件修飾符 (Event Modifiers)

Vue 提供了多個修飾符來處理常見的事件細節，讓我們專注於業務邏輯。

### 最大運算邏輯：互動式儀表板（攔截與冒泡）

```html
<div id="app">
    <!-- .prevent：阻止預設行為 (例如 Form Submit 或 Link Jump) -->
    <form @@submit.prevent="handleForm">
        <input type="text" v-model="formData" placeholder="輸入資料後按 Enter">
        <button type="submit">送出</button>
    </form>

    <!-- .stop：阻止冒泡 (Stop Propagation) -->
    <!-- 點擊內部按鈕只會觸發 btnClick，不會觸發 divClick -->
    <div class="outer" @@click="divClick" style="padding: 20px; border: 1px solid black;">
        點擊外部
        <button @@click.stop="btnClick">點擊內部</button>
    </div>

    <!-- .once：事件僅執行一次 -->
    <button @@click.once="onlyOnce">領取限時優惠</button>
</div>

<script>
    Vue.createApp({
        data() { return { formData: "" }; },
        methods: {
            handleForm() { console.log("表單已通過 .prevent 攔截，準備進行非同步提交..."); },
            divClick() { console.log("DIV 點擊事件觸發"); },
            btnClick() { console.log("按鈕點擊事件觸發，已使用 .stop 阻止冒泡"); },
            onlyOnce() { alert("恭喜領取成功！"); }
        }
    }).mount('#app');
</script>
```

## 4.3 鍵盤與滑鼠修飾符

精確控制使用者按下哪個按鍵或滑鼠按鈕。

```html
<div id="app">
    <!-- 鍵盤修飾符：按 Enter 才觸發 -->
    <input @@keyup.enter="submitSearch" placeholder="搜尋...">

    <!-- 組合鍵修飾符：按住 Shift + 點擊 -->
    <div @@click.shift="specialAction" class="box">
        按住 Shift 再點擊我
    </div>

    <!-- 滑鼠修飾符：右鍵點擊 -->
    <button @@click.right.prevent="showContextMenu">右鍵菜單</button>
</div>

<script>
    Vue.createApp({
        methods: {
            submitSearch() { console.log("執行關鍵字檢索運算..."); },
            specialAction() { alert("觸發特殊指令：管理員模式"); },
            showContextMenu() { console.log("顯示自定義右鍵選單"); }
        }
    }).mount('#app');
</script>
```

---

## 思考練習
1. 在 Razor 視圖中，為什麼縮寫語法 `@@click.stop` 必須加兩個 `@`？如果漏掉一個會發生什麼錯誤？
2. 使用修飾符 `.passive` 有什麼好處？它通常用於解決哪類效能問題？（提示：涉及滾動事件與行動裝置優化）。
