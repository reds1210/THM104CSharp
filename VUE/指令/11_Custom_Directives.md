# Chapter 11：自定義指令 (Custom Directives)

當 Vue 的內建指令（如 `v-if`, `v-model`）不足以應對某些低層次的 DOM 操作時，我們可以建立自己的指令。

## 11.1 指令的生命週期 (Lifecycle Hooks)

一個自定義指令的定義物件可以提供多個鉤子函數，這與組件生命週期類似，但在指令內部運作：

- `created`：在綁定元素的屬性或事件監聽器被應用之前調用。
- `beforeMount`：當指令第一次綁定到元素並且在掛載父組件之前調用。
- `mounted`：**最常用**，在綁定元素的父組件掛載後調用。
- `updated`：在包含組件及其子組件更新後調用。

### 範例：自動聚焦 (v-focus)

```html
<div id="app">
    <h3>用戶登入</h3>
    <!-- 當頁面載入時，這份輸入框會自動獲得焦點 -->
    <input v-focus placeholder="請輸入帳號">
</div>

<script>
    Vue.createApp({
        directives: {
            // 定義一個局部指令 v-focus
            focus: {
                mounted(el) {
                    // el 是原生的 DOM 元素
                    el.focus();
                }
            }
        }
    }).mount('#app');
</script>
```

## 11.2 帶參數的指令：權限控制系統 (v-permission)

我們可以使用指令的 `value` 參數來實現更複雜的邏輯。

### 最大運算邏輯：角色權限過濾器

```html
<div id="app">
    <p>目前登入角色：<strong>{{ currentUserRole }}</strong></p>

    <!-- 只有 Admin 能看到此按鈕 -->
    <button v-permission="'Admin'">刪除資料庫</button>

    <!-- Admin 與 Manager 都能看到 -->
    <button v-permission="['Admin', 'Manager']">編輯文章</button>
</div>

<script>
    Vue.createApp({
        data() {
            return { currentUserRole: 'Manager' };
        },
        directives: {
            permission: {
                // mounted 鉤子接收 el (元素) 和 binding (指令傳遞的值)
                mounted(el, binding, vnode) {
                    const requiredRoles = binding.value;
                    // 從組件實體存取資料 (這是一個進階運算)
                    const userRole = vnode.ctx.data.currentUserRole;

                    const hasPermission = Array.isArray(requiredRoles) 
                        ? requiredRoles.includes(userRole)
                        : requiredRoles === userRole;

                    if (!hasPermission) {
                        // 如果沒有權限，直接從 DOM 中物理移除
                        el.style.display = 'none';
                        // 或是徹底移除：el.parentNode && el.parentNode.removeChild(el);
                    }
                }
            }
        }
    }).mount('#app');
</script>
```

## 11.3 為什麼要使用自定義指令？

1.  **直接操作 DOM**：如初始化第三方套件（如 jQuery 插件、圖表庫）。
2.  **封裝重複行為**：如點擊外部關閉彈窗 (`v-click-outside`)、圖片懶加載 (`v-lazy`)。
3.  **語義化 UI 邏輯**：如上述的 `v-permission` 讓 HTML 結構一目了然。

---

## 思考練習
1. 在 `v-permission` 範例中，為什麼我們選擇在 `mounted` 階段操作 DOM 而不是 `created`？（提示：元素何時被插入到文件流中？）。
2. 指令的鉤子函數參數 `binding` 包含了哪些資訊？（提示：`name`, `value`, `oldValue`, `arg`, `modifiers`）。
3. 如果你想全局（Global）註冊一個指令，讓所有組件都能使用，應該如何調用 `app.directive()`？
