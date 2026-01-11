<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Dodge Game</title>
    <style>
        body { margin: 0; overflow: hidden; background: #222; font-family: 'Helvetica Neue', Arial, sans-serif; }
        canvas { display: block; background: #000; margin: 0 auto; }
        
        /* 説明画面のスタイル */
        #startScreen {
            position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.8); color: white;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            z-index: 10;
        }
        h1 { margin-bottom: 10px; color: #00FF00; }
        p { margin-bottom: 30px; font-size: 18px; }
        
        /* GOボタンのスタイル */
        #goButton {
            padding: 15px 40px; font-size: 24px; font-weight: bold;
            color: white; background: #ff4757; border: none; border-radius: 50px;
            cursor: pointer; box-shadow: 0 5px #ff6b81; outline: none;
            -webkit-tap-highlight-color: transparent;
        }
        #goButton:active { transform: translateY(3px); box-shadow: 0 2px #ff6b81; }

        #ui { position: absolute; top: 10px; left: 10px; color: white; font-size: 20px; pointer-events: none; display: none; }
    </style>
</head>
<body>

    <div id="startScreen">
        <h1>DODGE GAME</h1>
        <p>障害物を指やマウスで避けてください！</p>
        <button id="goButton">GO!</button>
    </div>

    <div id="ui">Score: 0</div>
    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        const scoreElement = document.getElementById("ui");
        const startScreen = document.getElementById("startScreen");
        const goButton = document.getElementById("goButton");

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        let score = 0;
        let gameActive = false; // 最初はオフ
        let player = { x: canvas.width/2, y: canvas.height - 80, size: 30, color: "#00FF00" };
        let enemies = [];

        // GOボタンを押した時の処理
        goButton.addEventListener("click", () => {
            startScreen.style.display = "none"; // 説明を隠す
            scoreElement.style.display = "block"; // スコアを表示
            gameActive = true;
            spawnEnemy();
            update();
        });

        // 操作設定（マウス＆タッチ）
        function move(e) {
            if(!gameActive) return;
            const clientX = e.touches ? e.touches[0].clientX : e.clientX;
            player.x = clientX - player.size / 2;
        }
        window.addEventListener("mousemove", move);
        window.addEventListener("touchstart", move);
        window.addEventListener("touchmove", move);

        function spawnEnemy() {
            if(!gameActive) return;
            enemies.push({
                x: Math.random() * (canvas.width - 20),
                y: -30,
                size: 20 + Math.random() * 20,
                speed: 4 + Math.random() * 5
            });
            setTimeout(spawnEnemy, 500);
        }

        function update() {
            if(!gameActive) return;
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // プレイヤー描画
            ctx.fillStyle = player.color;
            ctx.fillRect(player.x, player.y, player.size, player.size);

            // 敵の移動と描画
            enemies.forEach((enemy, index) => {
                enemy.y += enemy.speed;
                ctx.fillStyle = "#ff4757";
                ctx.fillRect(enemy.x, enemy.y, enemy.size, enemy.size);

                // 衝突判定
                if (player.x < enemy.x + enemy.size && player.x + player.size > enemy.x &&
                    player.y < enemy.y + enemy.size && player.y + player.size > enemy.y) {
                    gameActive = false;
                    alert("Game Over! スコア: " + score);
                    location.reload(); // リロードして最初に戻る
                }

                if(enemy.y > canvas.height) {
                    enemies.splice(index, 1);
                    score++;
                    scoreElement.innerText = "Score: " + score;
                }
            });
            requestAnimationFrame(update);
        }
    </script>
</body>
</html>
