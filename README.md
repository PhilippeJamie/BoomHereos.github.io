<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Mini Brawler</title>
  <style>
    canvas { background: #222; display: block; margin: auto; }
  </style>
</head>
<body>
<canvas id="game" width="800" height="600"></canvas>
<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let keys = {};
let bullets = [];

const player = {
  x: 400,
  y: 300,
  size: 20,
  speed: 3
};

const enemy = {
  x: 600,
  y: 300,
  size: 25
};

document.addEventListener("keydown", e => keys[e.key] = true);
document.addEventListener("keyup", e => keys[e.key] = false);
canvas.addEventListener("click", e => {
  const rect = canvas.getBoundingClientRect();
  const mouseX = e.clientX - rect.left;
  const mouseY = e.clientY - rect.top;
  const angle = Math.atan2(mouseY - player.y, mouseX - player.x);
  bullets.push({
    x: player.x,
    y: player.y,
    dx: Math.cos(angle) * 5,
    dy: Math.sin(angle) * 5
  });
});

function update() {
  // Bewegung
  if (keys["w"]) player.y -= player.speed;
  if (keys["s"]) player.y += player.speed;
  if (keys["a"]) player.x -= player.speed;
  if (keys["d"]) player.x += player.speed;

  // Projektile
  bullets.forEach(b => {
    b.x += b.dx;
    b.y += b.dy;
  });

  // Kollision mit Enemy
  bullets = bullets.filter(b => {
    const dx = b.x - enemy.x;
    const dy = b.y - enemy.y;
    const dist = Math.sqrt(dx*dx + dy*dy);
    return dist > player.size + enemy.size;
  });
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Spieler zeichnen
  ctx.fillStyle = "cyan";
  ctx.beginPath();
  ctx.arc(player.x, player.y, player.size, 0, Math.PI * 2);
  ctx.fill();

  // Gegner zeichnen
  ctx.fillStyle = "red";
  ctx.beginPath();
  ctx.arc(enemy.x, enemy.y, enemy.size, 0, Math.PI * 2);
  ctx.fill();

  // Projektile zeichnen
  ctx.fillStyle = "yellow";
  bullets.forEach(b => {
    ctx.beginPath();
    ctx.arc(b.x, b.y, 5, 0, Math.PI * 2);
    ctx.fill();
  });
}

function loop() {
  update();
  draw();
  requestAnimationFrame(loop);
}

loop();
</script>
</body>
</html>
