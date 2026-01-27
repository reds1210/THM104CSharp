# 終極指南：從零打造原生 JavaScript 貪食蛇 (ES5 Canvas 版)

這是一份完整的實作教程。我們將不依賴任何現代框架（如 React, Vue），也不使用新語法（如 Arrow Function, const/let），而是回歸最純粹的 **ECMAScript 5** 與 **HTML5 Canvas**。這能幫助你徹底理解程式運作的底層邏輯。

---

## 目錄 (Table of Contents)

1.  **[第一章：創世紀 - 環境與畫布](#第一章創世紀---環境與畫布)**
    *   理解 Canvas 座標系
    *   建立 HTML 骨架
2.  **[第二章：蛇的解剖學 - 資料結構與繪製](#第二章蛇的解剖學---資料結構與繪製)**
    *   為什麼是陣列？
    *   將資料轉化為像素
3.  **[第三章：生命的律動 - 遊戲迴圈與移動](#第三章生命的律動---遊戲迴圈與移動)**
    *   動畫原理：清除與重繪
    *   履帶式移動邏輯 (`unshift` & `pop`)
4.  **[第四章：意志的延伸 - 操控與防呆](#第四章意志的延伸---操控與防呆)**
    *   鍵盤事件監聽
    *   防止自殺邏輯
5.  **[第五章：生存法則 - 食物與成長](#第五章生存法則---食物與成長)**
    *   網格對齊的隨機數學
    *   成長機制
6.  **[第六章：死亡邊界 - 碰撞檢測](#第六章死亡邊界---碰撞檢測)**
    *   牆壁判定
    *   自噬判定
7.  **[附錄：完整程式碼對照](#附錄完整程式碼對照)**

---

## 第一章：創世紀 - 環境與畫布

在一切開始之前，我們需要一張「紙」和一支「筆」。

### 1.1 觀念：Canvas 座標系統
電腦繪圖的座標系與數學課本不同。
*   **(0, 0)** 原點位於 **左上角**。
*   **X 軸** 向右增加。
*   **Y 軸** 向下增加（這是最常搞錯的地方）。

```text
(0,0)  X軸 -> (+)
  +----------------------+
  |                      |
  |      P(100, 50)      |
Y |        *             |
軸|                      |
| |                      |
v |                      |
(+)                      |
  +----------------------+ (400, 400)
```

### 1.2 實作步驟
請建立一個檔案 `index.html`，並貼入以下內容。這包含了 CSS 美化與 Canvas 宣告。

```html
<!DOCTYPE html>
<html>
<head>
    <title>原生 JS 貪食蛇</title>
    <style>
        /* CSS 讓遊戲畫面置中，背景全黑 */
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #222;
            margin: 0;
        }
        canvas {
            border: 4px solid white;
            background-color: black;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.2);
        }
    </style>
</head>
<body>
    <!-- 遊戲舞台：寬高 400px -->
    <canvas id="gameCanvas" width="400" height="400"></canvas>

    <script>
        // --- [第一章程式碼] ---
        
        // 1. 抓取 Canvas 元素
        var gameCanvas = document.getElementById("gameCanvas");
        
        // 2. 取得 2D 繪圖環境 (這就是我們的畫筆)
        // 之後所有繪圖指令如 fillRect 都是由 ctx 執行
        var ctx = gameCanvas.getContext("2d");

        // 定義遊戲的基本單位：一格 20px
        var blockSize = 20;

        console.log("畫布準備完成！");
    </script>
</body>
</html>
```

> **🎯 驗收點**：打開瀏覽器，你應該看到螢幕中間有一個帶有白框的黑色正方形。

---

## 第二章：蛇的解剖學 - 資料結構與繪製

蛇在螢幕上是綠色的方塊，但在記憶體中，它是一串 **座標數據**。

### 2.1 觀念：陣列 (Array)
我們使用陣列來儲存蛇的每一節身體。
*   `snake[0]` 是 **頭**。
*   `snake[length-1]` 是 **尾**。
*   初始狀態下，我們讓蛇水平躺在畫布中央。

```javascript
// 記憶體中的蛇
var snake = [
  {x: 200, y: 200}, // 頭
  {x: 180, y: 200}, // 身體
  {x: 160, y: 200}, // 身體
  {x: 140, y: 200}, // 身體
  {x: 120, y: 200}  // 尾
];
```

### 2.2 實作步驟
在 `<script>` 中加入變數定義與繪圖函式。

```javascript
        // --- [第二章程式碼] 請接在 var blockSize = 20; 之後 ---

        // 定義蛇的初始狀態
        var snake = [
            {x: 200, y: 200},
            {x: 180, y: 200},
            {x: 160, y: 200},
            {x: 140, y: 200},
            {x: 120, y: 200}
        ];

        // 畫「一節」蛇的函式
        function drawSnakePart(snakePart) {
            ctx.fillStyle = 'lightgreen';  // 內部顏色
            ctx.strokeStyle = 'darkgreen'; // 邊框顏色
            
            // fillRect(x, y, 寬, 高) -> 填滿
            ctx.fillRect(snakePart.x, snakePart.y, blockSize, blockSize);
            // strokeRect(x, y, 寬, 高) -> 畫線
            ctx.strokeRect(snakePart.x, snakePart.y, blockSize, blockSize);
        }

        // 畫「整條」蛇的函式
        function drawSnake() {
            // 使用 forEach 遍歷陣列中的每一個座標物件
            snake.forEach(function(part) {
                drawSnakePart(part);
            });
        }

        // 立即呼叫一次，測試看看
        drawSnake();
```

> **🎯 驗收點**：重新整理，你會在黑色畫布中央看到一條靜止的綠色長條。

---

## 第三章：生命的律動 - 遊戲迴圈與移動

這章最重要。我們要讓蛇動起來。

### 3.1 觀念：履帶式移動 (Caterpillar Effect)
初學者常以為移動蛇需要更新每一節的座標。其實不用！
我們只需要做兩件事，就能產生移動的錯覺：
1.  **加頭 (`unshift`)**：根據目前速度算出下一格在哪，加到陣列最前面。
2.  **去尾 (`pop`)**：把陣列最後一個元素刪掉。

這樣，蛇依然保持 5 格長，但整體位置向前移了一格。

### 3.2 觀念：遊戲迴圈 (Game Loop)
電影是每秒 24 張靜態圖片。遊戲也是。
我們需要一個 `main` 函式，不斷執行：**清空畫面 -> 計算新位置 -> 繪製**。

### 3.3 實作步驟
請將剛才測試用的 `drawSnake();` 刪除，並加入以下程式碼：

```javascript
        // --- [第三章程式碼] ---

        // 速度變數：一開始往右跑 (X軸 +20)
        var dx = 20;
        var dy = 0;

        // 主循環函式
        function main() {
            // setTimeout 設定 100 毫秒後執行裡面的 function
            setTimeout(function onTick() {
                clearCanvas();    // 1. 擦黑板
                advanceSnake();   // 2. 算數學 (移動)
                drawSnake();      // 3. 畫圖畫
                
                // 遞迴：自己呼叫自己，形成無限循環
                main();
            }, 100);
        }

        // 清除畫布函式
        function clearCanvas() {
            ctx.fillStyle = "black";
            ctx.strokeStyle = "white";
            ctx.fillRect(0, 0, gameCanvas.width, gameCanvas.height);
            ctx.strokeRect(0, 0, gameCanvas.width, gameCanvas.height);
        }

        // 移動邏輯函式
        function advanceSnake() {
            // 算出新頭的位置 = 舊頭 + 速度
            var head = {x: snake[0].x + dx, y: snake[0].y + dy};
            
            // 加頭
            snake.unshift(head);
            
            // 去尾
            snake.pop();
        }

        // 啟動引擎！
        main();
```

> **🎯 驗收點**：重新整理，蛇會自動向右移動，然後穿過牆壁消失。

---

## 第四章：意志的延伸 - 操控與防呆

現在我們要用鍵盤控制它。

### 4.1 觀念：防自殺機制
蛇不能「原地掉頭」。
*   如果蛇正在往 **右** (`dx = 20`)，玩家按 **左** (`dx = -20`)。
*   下一個 Frame，頭會直接跟頸部重疊 -> 判定死亡。
*   **解法**：在接受按鍵指令前，檢查「目前是否正在反方向移動」。

### 4.2 實作步驟
加入鍵盤監聽與邏輯判斷。

```javascript
        // --- [第四章程式碼] ---

        function changeDirection(event) {
            var LEFT_KEY = 37;
            var RIGHT_KEY = 39;
            var UP_KEY = 38;
            var DOWN_KEY = 40;

            // 取得按下的鍵碼
            var keyPressed = event.keyCode;
            
            // 判斷目前的動向
            var goingUp = dy === -20;
            var goingDown = dy === 20;
            var goingRight = dx === 20;
            var goingLeft = dx === -20;

            // 邏輯：按下左鍵 且 不是正在往右走 -> 才能往左
            if (keyPressed === LEFT_KEY && !goingRight) {
                dx = -20;
                dy = 0;
            }
            if (keyPressed === UP_KEY && !goingDown) {
                dx = 0;
                dy = -20;
            }
            if (keyPressed === RIGHT_KEY && !goingLeft) {
                dx = 20;
                dy = 0;
            }
            if (keyPressed === DOWN_KEY && !goingUp) {
                dx = 0;
                dy = 20;
            }
        }

        // 向瀏覽器註冊事件監聽
        document.addEventListener("keydown", changeDirection);
```

> **🎯 驗收點**：現在你可以用方向鍵控制蛇了！試著不要讓它撞牆（雖然現在撞牆也不會怎樣）。

---

## 第五章：生存法則 - 食物與成長

### 5.1 觀念：網格對齊演算法
食物不能隨機出現在 `x=123` 這種位置，它必須對齊 `20` 的倍數。
公式：
`Math.round( 隨機數 / 20 ) * 20`
*   先除以20縮小範圍。
*   四捨五入取整數。
*   再乘以20還原。

### 5.2 觀念：成長機制
還記得移動是「加頭、去尾」嗎？
如果我們「**加頭、不去尾**」，蛇的長度不就 +1 了嗎？這就是成長的原理。

### 5.3 實作步驟
我們需要新增食物變數、生成函式，並 **修改** 原本的 `advanceSnake` 和 `main`。

**新增：**
```javascript
        // --- [第五章程式碼：新增部分] ---
        
        var foodX;
        var foodY;
        var score = 0;

        // 產生 min ~ max 之間的隨機亂數 (以 20 為單位)
        function randomTen(min, max) {
            return Math.round((Math.random() * (max-min) + min) / 20) * 20;
        }

        function createFood() {
            // 畫布寬 400，最大格子座標是 380 (因為 400 就出界了)
            foodX = randomTen(0, gameCanvas.width - 20);
            foodY = randomTen(0, gameCanvas.height - 20);
            
            // (選擇性挑戰：你可以寫一個迴圈檢查食物有沒有生在蛇身上，如果有就重產一次)
        }

        function drawFood() {
            ctx.fillStyle = 'red';
            ctx.strokeStyle = 'darkred';
            ctx.fillRect(foodX, foodY, blockSize, blockSize);
            ctx.strokeRect(foodX, foodY, blockSize, blockSize);
        }
```

**修改 `advanceSnake` (請完全取代舊的)：**
```javascript
        // --- [第五章程式碼：修改 advanceSnake] ---
        function advanceSnake() {
            var head = {x: snake[0].x + dx, y: snake[0].y + dy};
            
            // 1. 加頭
            snake.unshift(head);
            
            // 2. 判斷有沒有吃到食物
            var didEatFood = snake[0].x === foodX && snake[0].y === foodY;
            
            if (didEatFood) {
                // 吃到：分數+10，產生新食物，【不去尾】(蛇變長)
                score += 10;
                // 可以選擇性在這裡印出分數
                console.log("Score: " + score);
                createFood();
            } else {
                // 沒吃到：正常去尾
                snake.pop();
            }
        }
```

**修改 `main` (加入 `drawFood`)：**
```javascript
        // 在 main 函式裡面的 setTimeout 裡，加入 drawFood();
        /* 
           clearCanvas();
           drawFood();    <-- 記得加這行
           advanceSnake();
           drawSnake();
        */
```

**最後，記得在程式啟動時先產一顆食物：**
```javascript
        createFood();
        main(); // 原本的 main
```

> **🎯 驗收點**：畫面上出現紅色方塊。吃到它，蛇會變長，紅色方塊會瞬移。

---

## 第六章：死亡邊界 - 碰撞檢測

最後一步，讓遊戲有結束條件。

### 6.1 觀念
遊戲結束有兩種情況：
1.  **撞牆**：頭的座標 < 0 或 > 畫布邊界。
2.  **撞自己**：頭的座標 == 身體某一節的座標。

### 6.2 實作步驟
加入 `didGameEnd` 函式，並在 `main` 迴圈的一開始呼叫它。

```javascript
        // --- [第六章程式碼] ---

        function didGameEnd() {
            // 為了簡潔，先抓出頭
            var head = snake[0];

            // 1. 檢查撞牆
            var hitLeftWall = head.x < 0;
            var hitRightWall = head.x > gameCanvas.width - 20;
            var hitTopWall = head.y < 0;
            var hitBottomWall = head.y > gameCanvas.height - 20;

            // 2. 檢查撞自己
            // 為什麼從 i=4 開始？因為頭不可能撞到前三節，除非逆向 (但被 changeDirection 擋掉了)
            for (var i = 4; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    return true; // 撞到了
                }
            }

            return hitLeftWall || hitRightWall || hitTopWall || hitBottomWall;
        }

        // 修改 main 函式 (最終版)
        function main() {
            // 每次跑之前先檢查死了沒
            if (didGameEnd()) {
                alert("Game Over! 你的分數是: " + score);
                return; // 直接結束函式，不再呼叫 setTimeout
            }

            setTimeout(function onTick() {
                clearCanvas();
                drawFood();
                advanceSnake();
                drawSnake();
                main();
            }, 100);
        }
```

---

## 附錄：完整程式碼對照

如果你在中間迷路了，這是最終整合好的 `index.html` 內容。

```html
<!DOCTYPE html>
<html>
<head>
    <title>原生 JS 貪食蛇 (ES5 完整版)</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #222;
            margin: 0;
            color: white;
            font-family: Arial, sans-serif;
        }
        canvas {
            border: 4px solid white;
            background-color: black;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.2);
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="400" height="400"></canvas>

    <script>
        var gameCanvas = document.getElementById("gameCanvas");
        var ctx = gameCanvas.getContext("2d");
        var blockSize = 20;

        var snake = [
            {x: 200, y: 200},
            {x: 180, y: 200},
            {x: 160, y: 200},
            {x: 140, y: 200},
            {x: 120, y: 200}
        ];

        var dx = 20;
        var dy = 0;
        var foodX;
        var foodY;
        var score = 0;
        var changingDirection = false; // 進階優化：防止同一個 Frame 快速按兩次鍵

        createFood();
        main();
        document.addEventListener("keydown", changeDirection);

        function main() {
            if (didGameEnd()) {
                alert("Game Over! Score: " + score);
                return;
            }
            
            changingDirection = false;

            setTimeout(function onTick() {
                clearCanvas();
                drawFood();
                advanceSnake();
                drawSnake();
                main();
            }, 100);
        }

        function clearCanvas() {
            ctx.fillStyle = "black";
            ctx.strokeStyle = "white";
            ctx.fillRect(0, 0, gameCanvas.width, gameCanvas.height);
            ctx.strokeRect(0, 0, gameCanvas.width, gameCanvas.height);
        }

        function drawSnake() {
            snake.forEach(function(part) {
                ctx.fillStyle = 'lightgreen';
                ctx.strokeStyle = 'darkgreen';
                ctx.fillRect(part.x, part.y, blockSize, blockSize);
                ctx.strokeRect(part.x, part.y, blockSize, blockSize);
            });
        }

        function advanceSnake() {
            var head = {x: snake[0].x + dx, y: snake[0].y + dy};
            snake.unshift(head);

            var didEatFood = snake[0].x === foodX && snake[0].y === foodY;
            if (didEatFood) {
                score += 10;
                createFood();
            } else {
                snake.pop();
            }
        }

        function changeDirection(event) {
            if (changingDirection) return;
            changingDirection = true;

            var LEFT_KEY = 37;
            var RIGHT_KEY = 39;
            var UP_KEY = 38;
            var DOWN_KEY = 40;
            var keyPressed = event.keyCode;
            
            var goingUp = dy === -20;
            var goingDown = dy === 20;
            var goingRight = dx === 20;
            var goingLeft = dx === -20;

            if (keyPressed === LEFT_KEY && !goingRight) { dx = -20; dy = 0; }
            if (keyPressed === UP_KEY && !goingDown) { dx = 0; dy = -20; }
            if (keyPressed === RIGHT_KEY && !goingLeft) { dx = 20; dy = 0; }
            if (keyPressed === DOWN_KEY && !goingUp) { dx = 0; dy = 20; }
        }

        function randomTen(min, max) {
            return Math.round((Math.random() * (max-min) + min) / 20) * 20;
        }

        function createFood() {
            foodX = randomTen(0, gameCanvas.width - 20);
            foodY = randomTen(0, gameCanvas.height - 20);
            snake.forEach(function(part) {
                if (part.x === foodX && part.y === foodY) createFood();
            });
        }

        function drawFood() {
            ctx.fillStyle = 'red';
            ctx.strokeStyle = 'darkred';
            ctx.fillRect(foodX, foodY, blockSize, blockSize);
            ctx.strokeRect(foodX, foodY, blockSize, blockSize);
        }

        function didGameEnd() {
            var head = snake[0];
            for (var i = 4; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) return true;
            }
            return head.x < 0 || head.x > gameCanvas.width - 20 || head.y < 0 || head.y > gameCanvas.height - 20;
        }
    </script>
</body>
</html>
```

恭喜！你已經從零開始，使用最純正的 JavaScript 打造了一款經典遊戲。這不僅僅是完成了一個專案，更是對程式邏輯、座標系統與陣列操作的一次深度實踐。
