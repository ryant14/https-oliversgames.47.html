# https-oliversgames.47.html
<!DOCTYPE html>
<html>
<head>
  <title>My Website</title>
</head>
<body>
  <h1>Hello World!</h1>
  <p>This is my GitHub website.</p>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Click the Dot!</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      display: grid;
      place-items: center;
      height: 100vh;
      background: #f5f5f5;
    }
    .box {
      width: 360px;
      background: white;
      border-radius: 14px;
      padding: 16px;
      box-shadow: 0 8px 25px rgba(0,0,0,0.12);
    }
    h1 { margin: 0 0 8px; font-size: 22px; }
    .stats { display: flex; justify-content: space-between; margin-bottom: 10px; }
    button {
      width: 100%;
      padding: 10px;
      border: none;
      border-radius: 10px;
      background: #111;
      color: white;
      font-size: 16px;
      cursor: pointer;
    }
    button:disabled { opacity: 0.5; cursor: not-allowed; }
    #arena {
      position: relative;
      height: 260px;
      margin-top: 12px;
      border: 2px dashed #ddd;
      border-radius: 12px;
      background: #fafafa;
      overflow: hidden;
    }
    #dot {
      position: absolute;
      width: 36px;
      height: 36px;
      border-radius: 50%;
      background: #ff3b30;
      display: none;
      cursor: pointer;
    }
    .tip { margin-top: 10px; color: #444; font-size: 14px; }
  </style>
</head>
<body>
  <div class="box">
    <h1>🎯 Click the Dot</h1>

    <div class="stats">
      <div>Score: <strong id="score">0</strong></div>
      <div>Time: <strong id="time">30</strong>s</div>
    </div>

    <button id="startBtn">Start Game</button>

    <div id="arena">
      <div id="dot" title="Click me!"></div>
    </div>

    <div class="tip">Click the red dot as many times as you can in 30 seconds.</div>
  </div>

  <script>
    const startBtn = document.getElementById("startBtn");
    const arena = document.getElementById("arena");
    const dot = document.getElementById("dot");
    const scoreEl = document.getElementById("score");
    const timeEl = document.getElementById("time");

    let score = 0;
    let timeLeft = 30;
    let timer = null;
    let playing = false;

    function randomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    function moveDot() {
      const arenaRect = arena.getBoundingClientRect();
      const dotSize = 36;

      const maxX = arenaRect.width - dotSize;
      const maxY = arenaRect.height - dotSize;

      dot.style.left = randomInt(0, Math.max(0, Math.floor(maxX))) + "px";
      dot.style.top  = randomInt(0, Math.max(0, Math.floor(maxY))) + "px";
    }

    function startGame() {
      score = 0;
      timeLeft = 30;
      playing = true;

      scoreEl.textContent = score;
      timeEl.textContent = timeLeft;

      dot.style.display = "block";
      moveDot();

      startBtn.disabled = true;
      startBtn.textContent = "Game Running...";

      timer = setInterval(() => {
        timeLeft--;
        timeEl.textContent = timeLeft;

        if (timeLeft <= 0) {
          endGame();
        }
      }, 1000);
    }

    function endGame() {
      playing = false;
      clearInterval(timer);
      timer = null;

      dot.style.display = "none";

      startBtn.disabled = false;
      startBtn.textContent = "Play Again";

      alert("Time's up! Your score: " + score);
    }

    dot.addEventListener("click", () => {
      if (!playing) return;
      score++;
      scoreEl.textContent = score;
      moveDot();
    });

    startBtn.addEventListener("click", startGame);
  </script>
</body>
</html>

 <!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Catch the Star</title>
  <style>
    body {
      margin: 0;
      height: 100vh;
      display: grid;
      place-items: center;
      background: #0b1020;
      color: white;
      font-family: Arial, sans-serif;
    }
    .wrap {
      width: 420px;
      max-width: 92vw;
    }
    h1 { margin: 0 0 10px; font-size: 22px; }
    .hud {
      display: flex;
      justify-content: space-between;
      margin-bottom: 10px;
      opacity: 0.95;
    }
    button {
      width: 100%;
      margin: 10px 0 0;
      padding: 10px;
      border: 0;
      border-radius: 12px;
      background: #ffffff;
      color: #0b1020;
      font-size: 16px;
      cursor: pointer;
      font-weight: bold;
    }
    canvas {
      width: 100%;
      height: 460px;
      background: linear-gradient(#0b1020, #121a35);
      border: 2px solid rgba(255,255,255,0.15);
      border-radius: 16px;
      display: block;
    }
    .tip { margin-top: 8px; font-size: 13px; opacity: 0.8; }
  </style>
</head>
<body>
  <div class="wrap">
    <h1>⭐ Catch the Falling Star</h1>

    <div class="hud">
      <div>Score: <strong id="score">0</strong></div>
      <div>Misses: <strong id="misses">0</strong>/3</div>
    </div>

    <canvas id="game" width="420" height="460"></canvas>
    <button id="startBtn">Start</button>

    <div class="tip">Use ← → (or A / D). Catch stars. Miss 3 and it’s over.</div>
  </div>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    const scoreEl = document.getElementById("score");
    const missesEl = document.getElementById("misses");
    const startBtn = document.getElementById("startBtn");

    let running = false;
    let score = 0;
    let misses = 0;

    const player = {
      w: 70,
      h: 14,
      x: (canvas.width - 70) / 2,
      y: canvas.height - 30,
      speed: 6,
      vx: 0
    };

    let star = null;

    function resetStar() {
      star = {
        x: Math.random() * (canvas.width - 18) + 9,
        y: -12,
        r: 10,
        vy: 3 + Math.min(6, score * 0.2) // gets faster as you score
      };
    }

    function resetGame() {
      score = 0;
      misses = 0;
      scoreEl.textContent = score;
      missesEl.textContent = misses;

      player.x = (canvas.width - player.w) / 2;
      player.vx = 0;

      resetStar();
    }

    function drawBackground() {
      // simple little “stars” in the background
      for (let i = 0; i < 40; i++) {
        const x = (i * 97) % canvas.width;
        const y = (i * 53) % canvas.height;
        ctx.globalAlpha = 0.18;
        ctx.fillStyle = "white";
        ctx.fillRect(x, y, 2, 2);
      }
      ctx.globalAlpha = 1;
    }

    function drawPlayer() {
      ctx.fillStyle = "rgba(255,255,255,0.95)";
      ctx.fillRect(player.x, player.y, player.w, player.h);
      ctx.globalAlpha = 0.25;
      ctx.fillStyle = "white";
      ctx.fillRect(player.x, player.y + player.h, player.w, 6);
      ctx.globalAlpha = 1;
    }

    function drawStar() {
      ctx.beginPath();
      ctx.fillStyle = "#ffd166";
      ctx.arc(star.x, star.y, star.r, 0, Math.PI * 2);
      ctx.fill();
      // tiny glow
      ctx.globalAlpha = 0.25;
      ctx.beginPath();
      ctx.arc(star.x, star.y, star.r + 8, 0, Math.PI * 2);
      ctx.fill();
      ctx.globalAlpha = 1;
    }

    function update() {
      if (!running) return;

      // move player
      player.x += player.vx;
      if (player.x < 0) player.x = 0;
      if (player.x + player.w > canvas.width) player.x = canvas.width - player.w;

      // move star
      star.y += star.vy;

      // collision check
      const hit =
        star.y + star.r >= player.y &&
        star.y - star.r <= player.y + player.h &&
        star.x >= player.x &&
        star.x <= player.x + player.w;

      if (hit) {
        score++;
        scoreEl.textContent = score;
        resetStar();
      }

      // missed star
      if (star.y - star.r > canvas.height) {
        misses++;
        missesEl.textContent = misses;
        resetStar();

        if (misses >= 3) {
          gameOver();
          return;
        }
      }

      // draw
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawBackground();
      drawStar();
      drawPlayer();

      requestAnimationFrame(update);
    }

    function startGame() {
      running = true;
      startBtn.disabled = true;
      startBtn.textContent = "Playing...";
      resetGame();
      requestAnimationFrame(update);
    }

    function gameOver() {
      running = false;
      startBtn.disabled = false;
      startBtn.textContent = "Play Again";
      alert("Game Over! Your score: " + score);
    }

    // controls
    window.addEventListener("keydown", (e) => {
      if (e.key === "ArrowLeft" || e.key.toLowerCase() === "a") player.vx = -player.speed;
      if (e.key === "ArrowRight" || e.key.toLowerCase() === "d") player.vx = player.speed;
    });

    window.addEventListener("keyup", (e) => {
      const k = e.key.toLowerCase();
      if (e.key === "ArrowLeft" || k === "a") if (player.vx < 0) player.vx = 0;
      if (e.key === "ArrowRight" || k === "d") if (player.vx > 0) player.vx = 0;
    });

    // mobile: move by tapping left/right side of canvas
    canvas.addEventListener("touchstart", (e) => {
      const rect = canvas.getBoundingClientRect();
      const touchX = e.touches[0].clientX - rect.left;
      player.vx = touchX < rect.width / 2 ? -player.speed : player.speed;
    }, { passive: true });

    canvas.addEventListener("touchend", () => {
      player.vx = 0;
    });

    startBtn.addEventListener("click", startGame);

    // show a starting screen
    ctx.fillStyle = "rgba(255,255,255,0.9)";
    ctx.font = "18px Arial";
    ctx.fillText("Press Start to play!", 120, 230);
  </script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Snake</title>
  <style>
    body {
      margin: 0;
      height: 100vh;
      display: grid;
      place-items: center;
      background: #111;
      color: #fff;
      font-family: Arial, sans-serif;
    }
    .wrap {
      width: 420px;
      max-width: 92vw;
      text-align: center;
    }
    h1 { margin: 0 0 10px; font-size: 22px; }
    .hud
 
