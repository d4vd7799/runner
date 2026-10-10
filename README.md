// game.js - Barebones Starter
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

const player = { x: 50, y: 200, width: 30, height: 30, dy: 0, gravity: 0.6, jumpPower: -12, grounded: false };
const obstacles = [];
let frame = 0;
let score = 0;
let gameOver = false;

// Input
document.addEventListener("keydown", (e) => {
  if (e.code === "Space" && player.grounded && !gameOver) {
    player.dy = player.jumpPower;
    player.grounded = false;
  }
});

function update() {
  if (gameOver) return;

  // Physics
  player.dy += player.gravity;
  player.y += player.dy;

  if (player.y + player.height >= canvas.height - 20) { // Ground at Y = height - 20
    player.y = canvas.height - 20 - player.height;
    player.dy = 0;
    player.grounded = true;
  }

  // Obstacle Spawner
  frame++;
  if (frame % 90 === 0) {
    obstacles.push({ x: canvas.width, y: canvas.height - 50, width: 20, height: 30 });
  }

  // Move & check collisions
  for (let i = obstacles.length - 1; i >= 0; i--) {
    obstacles[i].x -= 5;

    // AABB Collision Check
    if (
      player.x < obstacles[i].x + obstacles[i].width &&
      player.x + player.width > obstacles[i].x &&
      player.y < obstacles[i].y + obstacles[i].height &&
      player.y + player.height > obstacles[i].y
    ) {
      gameOver = true;
      alert("Game Over! Score: " + score);
    }

    if (obstacles[i].x + obstacles[i].width < 0) {
      obstacles.splice(i, 1);
      score += 10;
    }
  }
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Ground
  ctx.fillStyle = "#666";
  ctx.fillRect(0, canvas.height - 20, canvas.width, 20);

  // Player
  ctx.fillStyle = "#ff4757";
  ctx.fillRect(player.x, player.y, player.width, player.height);

  // Obstacles
  ctx.fillStyle = "#2ed573";
  obstacles.forEach(obs => ctx.fillRect(obs.x, obs.y, obs.width, obs.height));

  // Score
  ctx.fillStyle = "#000";
  ctx.font = "20px Arial";
  ctx.fillText(`Score: ${score}`, 10, 30);
}

function loop() {
  update();
  draw();
  if (!gameOver) requestAnimationFrame(loop);
}

loop();
