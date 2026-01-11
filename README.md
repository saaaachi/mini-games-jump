<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Simple Dodge Game</title>
    <style>
        body { margin: 0; overflow: hidden; background: #222; font-family: sans-serif; }
        canvas { display: block; background: #000; margin: 0 auto; }
        #ui { position: absolute; top: 10px; left: 10px; color: white; font-size: 20px; pointer-events: none; }
    </style>
</head>
<body>
    <div id="ui">Score: 0</div>
    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        const scoreElement = document.getElementById("ui");

        // 画面サイズ設定（スマホ対応）
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        let score = 0;
        let gameActive = true;
        let player = { x: canvas.width/2, y: canvas.height - 50, size: 30, color: "#00FF00" };
        let enemies = [];

        // 操作設定（マウス＆タッチ両方）
        function move(e) {
            const clientX = e.touches ? e.touches[0].clientX : e.clientX;
            player.x = clientX - player.size / 2;
        }
        window.addEventListener("mousemove", move);
        window.addEventListener("touchstart", move);
        window.addEventListener("touchmove", move);

        function spawnEnemy() {
            if(!gameActive) return;
            enemies.push({
                x: Math.random() * canvas.width,
                y: -30,
                size: 20 + Math.random() * 20,
                speed: 3 + Math.random() * 5
            });
            setTimeout(spawnEnemy, 600);
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
                ctx.fillStyle = "red";
                ctx.fillRect(enemy.x, enemy.y, enemy.size, enemy.size);

                // 衝突判定
                if (player.x < enemy.x + enemy.size && player.x + player.size > enemy.x &&
                    player.y < enemy.y + enemy.size && player.y + player.size > enemy.y) {
                    alert("Game Over! Score: " + score);
                    location.reload();
                    gameActive = false;
                }

                // 画面外に出たら消す
                if(enemy.y > canvas.height) {
                    enemies.splice(index, 1);
                    score++;
                    scoreElement.innerText = "Score: " + score;
                }
            });
            requestAnimationFrame(update);
        }

        spawnEnemy();
        update();
    </script>
</body>
</html># mini-games-jump
