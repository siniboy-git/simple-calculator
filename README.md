<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Car Racing Game</title>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  background: #111;
  font-family: Arial, sans-serif;
  overflow: hidden;
  text-align: center;
}

h1 {
  color: white;
  margin: 12px 0;
}

#game {
  position: relative;
  width: 400px;
  height: 600px;
  margin: auto;
  overflow: hidden;
  background: #333;
  border-left: 8px solid #555;
  border-right: 8px solid #555;
}

.roadLine {
  position: absolute;
  width: 8px;
  height: 80px;
  background: white;
  left: 50%;
  transform: translateX(-50%);
}

#player {
  position: absolute;
  width: 55px;
  height: 95px;
  bottom: 30px;
  left: 172px;
  background: red;
  border-radius: 12px;
  border: 4px solid #222;
  box-shadow: 0 0 10px red;
}

.enemy {
  position: absolute;
  width: 55px;
  height: 95px;
  background: yellow;
  border-radius: 12px;
  border: 4px solid #222;
}

#score {
  color: white;
  font-size: 22px;
  margin: 10px;
}

#gameOver {
  display: none;
  position: absolute;
  inset: 0;
  background: rgba(0,0,0,.8);
  color: white;
  padding-top: 230px;
  font-size: 30px;
  z-index: 10;
}

button {
  margin-top: 20px;
  padding: 12px 25px;
  font-size: 18px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}
</style>
</head>

<body>

<h1>🏎️ Car Racing Game</h1>
<div id="score">Score: 0</div>

<div id="game">

  <div class="roadLine" style="top:0"></div>
  <div class="roadLine" style="top:150px"></div>
  <div class="roadLine" style="top:300px"></div>
  <div class="roadLine" style="top:450px"></div>

  <div id="player"></div>

  <div id="gameOver">
    💥 GAME OVER
    <br>
    <span id="finalScore"></span>
    <br>
    <button onclick="restartGame()">Restart</button>
  </div>

</div>

<script>
const game = document.getElementById("game");
const player = document.getElementById("player");
const scoreText = document.getElementById("score");
const gameOver = document.getElementById("gameOver");
const finalScore = document.getElementById("finalScore");

let playerX = 172;
let score = 0;
let speed = 5;
let gameRunning = true;
let keys = {};

document.addEventListener("keydown", e => {
  keys[e.key] = true;
});

document.addEventListener("keyup", e => {
  keys[e.key] = false;
});

function createEnemy() {
  if (!gameRunning) return;

  const enemy = document.createElement("div");
  enemy.className = "enemy";

  const lanes = [45, 172, 300];
  enemy.style.left = lanes[Math.floor(Math.random() * lanes.length)] + "px";
  enemy.style.top = "-120px";

  game.appendChild(enemy);

  let y = -120;

  const moveEnemy = setInterval(() => {
    if (!gameRunning) {
      clearInterval(moveEnemy);
      return;
    }

    y += speed;
    enemy.style.top = y + "px";

    if (collision(player, enemy)) {
      endGame();
      clearInterval(moveEnemy);
    }

    if (y > 650) {
      enemy.remove();
      clearInterval(moveEnemy);
      score++;
      scoreText.innerText = "Score: " + score;

      if (score % 5 === 0) {
        speed += 0.5;
      }
    }
  }, 20);
}

function collision(a, b) {
  const r1 = a.getBoundingClientRect();
  const r2 = b.getBoundingClientRect();

  return !(
    r1.bottom < r2.top ||
    r1.top > r2.bottom ||
    r1.right < r2.left ||
    r1.left > r2.right
  );
}

function gameLoop() {
  if (!gameRunning) return;

  if (keys["ArrowLeft"] || keys["a"]) {
    playerX -= 6;
  }

  if (keys["ArrowRight"] || keys["d"]) {
    playerX += 6;
  }

  if (playerX < 10) playerX = 10;
  if (playerX > 335) playerX = 335;

  player.style.left = playerX + "px";

  // Road lines movement
  document.querySelectorAll(".roadLine").forEach(line => {
    let top = parseInt(line.style.top);
    top += speed;

    if (top > 600) top = -80;

    line.style.top = top + "px";
  });

  requestAnimationFrame(gameLoop);
}

function endGame() {
  gameRunning = false;
  finalScore.innerText = "Score: " + score;
  gameOver.style.display = "block";
}

function restartGame() {
  document.querySelectorAll(".enemy").forEach(e => e.remove());

  playerX = 172;
  score = 0;
  speed = 5;
  gameRunning = true;

  player.style.left = playerX + "px";
  scoreText.innerText = "Score: 0";
  gameOver.style.display = "none";

  gameLoop();
}

// Enemy cars
setInterval(createEnemy, 1200);

// Start game
gameLoop();
</script>

</body>
</html>

