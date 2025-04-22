<!DOCTYPE html> 
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>총알 피하기 + 번개 + 랭킹</title>
  <style>
    body {
      text-align: center;
      font-family: sans-serif;
    }
    #game {
      position: relative;
      width: 600px;
      height: 600px;
      background: #111;
      margin: 20px auto;
      overflow: hidden;
      border: 3px solid #333;
    }
    #player {
      position: absolute;
      bottom: 10px;
      width: 40px;
      height: 40px;
      background: lime;
      left: 280px;
    }
    .bullet {
      position: absolute;
      width: 10px;
      height: 20px;
      background: red;
    }
    .warning, .lightning {
      position: absolute;
      top: 0;
      width: 65px;
      height: 100%;
      z-index: 1;
    }
    .warning {
      background: rgba(255, 255, 0, 0.3);
    }
    .lightning {
      background: rgba(255, 255, 0, 0.9);
    }
    #startBtn {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 16px;
    }
    #timer {
      margin-top: 10px;
      font-size: 18px;
    }
    #rankBoard {
      margin-top: 20px;
    }
    #rankBoard h2 {
      margin-bottom: 5px;
    }
    #rankList {
      list-style: none;
      padding: 0;
      margin: 0 auto;
      width: fit-content;
      text-align: left;
      font-size: 16px;
    }
  </style>
</head>
<body>
  <h1>총알 피하기 + 번개</h1>
  <button id="startBtn">게임 시작</button>
  <div id="timer">시간: 0.00초</div>

  <div id="rankBoard">
    <h2>랭킹 TOP10</h2>
    <ol id="rankList"></ol>
  </div>

  <div id="game">
    <div id="player"></div>
  </div>

  <script>
    const game = document.getElementById("game");
    const player = document.getElementById("player");
    const timerEl = document.getElementById("timer");
    const startBtn = document.getElementById("startBtn");

    const gameWidth = 600;
    const playerWidth = 40;
    const playerTop = 560;

    let gameInterval, bulletTimer, timerInterval, lightningTimer;
    let startTime;
    let positionX = 280;
    let isPlaying = false;
    let bulletCount = 1;
    let bulletSpeed = 5;
    let pressedKeys = {};
    let lightningStart = false;

    function startGame() {
      isPlaying = true;
      positionX = 280;
      bulletCount = 1;
      bulletSpeed = 5;
      lightningStart = false;

      player.style.left = positionX + "px";
      game.querySelectorAll('.bullet, .warning, .lightning').forEach(el => el.remove());

      startTime = Date.now();
      startTimer();
      gameInterval = requestAnimationFrame(gameLoop);
      startBulletLoop();
    }

    function endGame() {
      isPlaying = false;
      cancelAnimationFrame(gameInterval);
      clearInterval(bulletTimer);
      clearInterval(timerInterval);
      clearInterval(lightningTimer);
      const currentTime = (Date.now() - startTime) / 1000;
      setTimeout(() => updateRankWithName(currentTime), 100);
    }

    function updateRankWithName(currentTime) {
      const name = prompt(`게임 오버! 이름을 입력하세요 (최대 10자):`);
      if (!name) return;

      let ranks = JSON.parse(localStorage.getItem("dodgeRanks") || "[]");
      ranks.push({ name: name.slice(0, 10), time: currentTime });
      ranks.sort((a, b) => b.time - a.time);
      ranks = ranks.slice(0, 10);
      localStorage.setItem("dodgeRanks", JSON.stringify(ranks));
      updateRankBoard();
    }

    function updateRankBoard() {
      const rankData = JSON.parse(localStorage.getItem("dodgeRanks") || "[]");
      const list = document.getElementById("rankList");
      list.innerHTML = "";
      rankData.forEach(r => {
        const li = document.createElement("li");
        li.textContent = `${r.name} - ${r.time.toFixed(2)}초`;
        list.appendChild(li);
      });
    }

    function startTimer() {
      timerInterval = setInterval(() => {
        const now = (Date.now() - startTime) / 1000;
        timerEl.textContent = `시간: ${now.toFixed(2)}초`;

        if (now >= 10) bulletCount = 2;
        if (now >= 30) bulletCount = 3;
        if (now >= 15 && now < 40) bulletSpeed = 8;
        if (now >= 40) bulletSpeed = 12;
        if (now >= 20 && !lightningStart) {
          lightningStart = true;
          lightningTimer = setInterval(triggerLightning, 1500);
        }
      }, 50);
    }

    function startBulletLoop() {
      bulletTimer = setInterval(createBullet, 300);
    }

    function createBullet() {
      for (let i = 0; i < bulletCount; i++) {
        const bullet = document.createElement("div");
        bullet.classList.add("bullet");
        bullet.style.left = Math.floor(Math.random() * (gameWidth - 10)) + "px";
        bullet.style.top = "-20px";
        game.appendChild(bullet);
      }
    }

    function triggerLightning() {
      const left = Math.floor(Math.random() * (gameWidth - 50));
      const warning = document.createElement("div");
      warning.classList.add("warning");
      warning.style.left = left + "px";
      game.appendChild(warning);

      setTimeout(() => {
        warning.remove();

        const lightning = document.createElement("div");
        lightning.classList.add("lightning");
        lightning.style.left = left + "px";
        game.appendChild(lightning);

        setTimeout(() => lightning.remove(), 350);

        const playerLeft = positionX;
        const playerRight = positionX + playerWidth;
        if (playerRight > left && playerLeft < left + 50) {
          endGame();
        }
      }, 1500);
    }

    function gameLoop() {
      if (!isPlaying) return;

      handleMovement();

      document.querySelectorAll(".bullet").forEach(bullet => {
        let top = parseInt(bullet.style.top);
        bullet.style.top = (top + bulletSpeed) + "px";

        const bulletLeft = parseInt(bullet.style.left);
        const bulletRight = bulletLeft + 10;
        const bulletBottom = top + 20;

        const playerLeft = positionX;
        const playerRight = positionX + playerWidth;

        if (
          bulletBottom >= playerTop &&
          bulletLeft < playerRight &&
          bulletRight > playerLeft
        ) {
          endGame();
        }

        if (top > 600) bullet.remove();
      });

      requestAnimationFrame(gameLoop);
    }

    function handleMovement() {
      if ((pressedKeys["ArrowLeft"] || pressedKeys["a"]) && positionX > 0) {
        positionX -= 6;
      }
      if ((pressedKeys["ArrowRight"] || pressedKeys["d"]) && positionX < gameWidth - playerWidth) {
        positionX += 6;
      }
      player.style.left = positionX + "px";
    }

    document.addEventListener("keydown", e => pressedKeys[e.key.toLowerCase()] = true);
    document.addEventListener("keyup", e => pressedKeys[e.key.toLowerCase()] = false);
    startBtn.addEventListener("click", startGame);

    updateRankBoard(); // 페이지 로드시 랭킹 표시
  </script>
</body>
</html>`
