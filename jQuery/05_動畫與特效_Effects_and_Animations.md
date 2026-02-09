# 第 5 章：動畫與特效 (Effects & Animations)

## 本章目標
讓網頁互動更生動，提升使用者體驗 (UX)。

## 教學大綱

### 5.1 內建視覺特效
- [ ] 顯示隱藏：`show()`, `hide()`, `toggle()`
- [ ] 滑動效果：`slideDown()`, `slideUp()`, `slideToggle()` (適合製作 FAQ 手風琴效果)
- [ ] 淡入淡出：`fadeIn()`, `fadeOut()`, `fadeToggle()`, `fadeTo()`

### 5.2 自定義動畫
- [ ] `animate({ params }, speed, callback)`
- [ ] 支援的屬性：數值型 CSS (width, height, opacity, left...)
- [ ] 累加/累減動畫：`left: '+=50px'`

### 5.3 動畫控制與隊列
- [ ] 動畫隊列 (Queue) 行為：連續點擊導致動畫播不完
- [ ] `stop(clearQueue, jumpToEnd)`：停止當前動畫 (解決滑鼠快速滑過閃爍問題)
- [ ] 鏈式動畫 (Chaining)：`.slideUp().slideDown()`

### 5.4 實作練習
- [ ] 練習 5：製作一個側邊導航欄 (Sidebar)。
    1. 點擊漢堡選單圖示，側邊欄 `animate` 滑出 (改變 `left` 或 `width`)。
    2. 同時內容區域變暗 (`fadeTo`)。
