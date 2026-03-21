# Chapter 09：頁面體驗與安全 - v-cloak

在使用 CDN 引入 Vue 的專案（特別是在 ASP.NET Core MVC 中）中，最常遇到的問題是：在 Vue 實體掛載完成前，使用者會看到原始的大括號 `{{ message }}` 代碼。這種現象被稱為 **Flash of Uncompiled Content (FOUC)**。

## 9.1 v-cloak 的運作原理

`v-cloak` 是一個特殊的指令，它會保持在元素上，直到關聯的 Vue 實體完成掛載（mounted）。

### 運算邏輯：配合 CSS 隱藏內容

```css
/* CSS 規則：選取所有帶有 v-cloak 屬性的元素並隱藏 */
[v-cloak] {
    display: none !important;
}
```

當 Vue 實體掛載完成後，它會自動將所有元素上的 `v-cloak` 屬性移除，內容隨即顯示。

## 9.2 實戰範例：專業的載入畫面

在 Razor 視圖中，我們可以使用 `v-cloak` 來實現流暢的載入體驗。

```html
<style>
    [v-cloak] { display: none; }
    
    /* 可選：自定義一個載入中的提示 */
    .loading-spinner {
        position: fixed;
        top: 50%;
        left: 50%;
    }
</style>

<div id="app">
    <!-- 這個區塊在 Vue 掛載前會完全隱藏 -->
    <div v-cloak class="main-content">
        <h1>{{ welcomeMessage }}</h1>
        <p>系統狀態：{{ serverStatus }}</p>
    </div>

    <!-- 靜態 HTML 部分：這會立即顯示，作為一種骨架屏 (Skeleton Screen) -->
    <div v-if="!isLoaded" class="loading-spinner">
        正在解析伺服器指令中...
    </div>
</div>

<script>
    Vue.createApp({
        data() {
            return {
                welcomeMessage: "歡迎使用 Vue 3 整合控制台",
                serverStatus: "運行中",
                isLoaded: false
            };
        },
        mounted() {
            // 模擬一些複雜的初始化運算
            setTimeout(() => {
                this.isLoaded = true;
                console.log("Vue 實體已掛載，v-cloak 屬性已由 Vue 自動移除。");
            }, 800);
        }
    }).mount('#app');
</script>
```

## 9.3 為什麼需要 v-cloak？

1.  **防止程式碼洩漏**：不讓使用者看到未處理的模板語法。
2.  **減少佈局跳動**：避免未渲染內容撐開高度後又縮小的跳動感。
3.  **安全性（隱喻層面）**：避免使用者誤以為網站出錯或被竄改。

---

## 思考練習
1. 除了 `v-cloak` 之外，還有哪些指令可以達到「防止大括號閃爍」的效果？（提示：複習 Chapter 02 的 `v-text`）。
2. 在 ASP.NET Core MVC 中，如果你將 Vue 的腳本放在頁面底部（`@section Scripts`），這對 `v-cloak` 的必要性有什麼影響？
    - 提示：考慮瀏覽器的渲染解析順序。
