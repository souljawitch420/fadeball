<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Stickman Basketball</title>
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #111;
      color: white;
      text-align: center;
    }

    h1 {
      margin: 15px 0 5px;
    }

    p {
      margin: 5px;
    }

    canvas {
      background: linear-gradient(#87ceeb 0%, #87ceeb 65%, #d28b45 65%, #d28b45 100%);
      border: 4px solid white;
      display: block;
      margin: 15px auto;
    }
  </style>
</head>
<body>
  <h1>Stickman Basketball</h1>
  <p>Move: A/D or Arrow Keys | Shoot: Space</p>
  <p>Score: <span id="score">0</span></p>

  <canvas id="game" width="800" height="450"></canvas>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");
    const scoreText = document.getElementById("score");

    let score = 0;
    const keys = {};

    const player = {
      x: 120,
      y: 330,
      speed: 5
    };

    const ball = {
      x: player.x + 25,
      y: player.y - 65,
      r: 10,
      vx: 0,
      vy: 0,
      shooting: false
    };

    const hoop = {
      x: 665,
      y: 170,
      w: 80,
      h: 12
    };

    document.addEventListener("keydown", (e) => {
      keys[e.key.toLowerCase()] = true;
      if (e.code === "Space") shootBall();
    });

    document.addEventListener("keyup", (e) => {
      keys[e.key.toLowerCase()] = false;
    });

    function shootBall() {
      if (!ball.shooting) {
        ball.shooting = true;
        ball.vx = 8;
        ball.vy = -12;
      }
    }

    function resetBall() {
      ball.shooting = false;
      ball.vx = 0;
      ball.vy = 0;
      ball.x = player.x + 25;
      ball.y = player.y - 65;
    }

    function update() {
      if (keys["a"] || keys["arrowleft"]) player.x -= player.speed;
      if (keys["d"] || keys["arrowright"]) player.x += player.speed;

      player.x = Math.max(40, Math.min(player.x, canvas.width - 120));

      if (!ball.shooting) {
        ball.x = player.x + 25;
        ball.y = player.y - 65;
      } else {
        ball.x += ball.vx;
        ball.y += ball.vy;
        ball.vy += 0.45;

        const scored =
          ball.x > hoop.x &&
          ball.x < hoop.x + hoop.w &&
          ball.y > hoop.y - 8 &&
          ball.y < hoop.y + 18 &&
          ball.vy > 0;

        if (scored) {
          score++;
          scoreText.textContent = score;
          resetBall();
        }

        if (ball.y > canvas.height || ball.x > canvas.width || ball.x < 0) {
          resetBall();
        }
      }
    }

    function drawStickman() {
      ctx.strokeStyle = "black";
      ctx.lineWidth = 4;

      // head
      ctx.beginPath();
      ctx.arc(player.x, player.y - 80, 18, 0, Math.PI * 2);
      ctx.stroke();

      // body
      ctx.beginPath();
      ctx.moveTo(player.x, player.y - 62);
      ctx.lineTo(player.x, player.y - 20);
      ctx.stroke();

      // arms
      ctx.beginPath();
      ctx.moveTo(player.x, player.y - 50);
      ctx.lineTo(player.x + 30, player.y - 65);
      ctx.moveTo(player.x, player.y - 50);
      ctx.lineTo(player.x - 25, player.y - 35);
      ctx.stroke();

      // legs
      ctx.beginPath();
      ctx.moveTo(player.x, player.y - 20);
      ctx.lineTo(player.x - 20, player.y + 20);
      ctx.moveTo(player.x, player.y - 20);
      ctx.lineTo(player.x + 25, player.y + 20);
      ctx.stroke();
    }

    function drawHoop() {
      // pole
      ctx.fillStyle = "gray";
      ctx.fillRect(735, 120, 8, 230);

      // backboard
      ctx.fillStyle = "white";
      ctx.fillRect(685, 120, 60, 50);
      ctx.strokeStyle = "black";
      ctx.strokeRect(685, 120, 60, 50);

      // rim
      ctx.strokeStyle = "orange";
      ctx.lineWidth = 5;
      ctx.beginPath();
      ctx.moveTo(hoop.x, hoop.y);
      ctx.lineTo(hoop.x + hoop.w, hoop.y);
      ctx.stroke();
    }

    function drawBall() {
      ctx.fillStyle = "orange";
      ctx.beginPath();
      ctx.arc(ball.x, ball.y, ball.r, 0, Math.PI * 2);
      ctx.fill();
      ctx.strokeStyle = "black";
      ctx.stroke();
    }

    function drawCourt() {
      ctx.strokeStyle = "white";
      ctx.lineWidth = 3;
      ctx.beginPath();
      ctx.moveTo(0, 360);
      ctx.lineTo(canvas.width, 360);
      ctx.stroke();
    }

    function gameLoop() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      update();
      drawCourt();
      drawHoop();
      drawStickman();
      drawBall();
      requestAnimationFrame(gameLoop);
    }

    gameLoop();
  </script>
</body>
</html>
