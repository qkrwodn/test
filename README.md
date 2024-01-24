<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>미사일 피하기 & 총 쏘기 게임</title>
<style>
  canvas {
    background-color: #f0f0f0;
    display: block;
    margin: 0 auto;
    border: 1px solid black;
  }
</style>
</head>
<body>

<canvas id="gameCanvas" width="900" height="700"></canvas>

<script>
  var canvas = document.getElementById('gameCanvas');
  var ctx = canvas.getContext('2d');

  var player = {
    x: canvas.width / 2,
    y: canvas.height - 60,
    width: 50,
    height: 50,
    color: '#00A'
  };

  var playerBullets = [];
  var missiles = [];
  var missileSpeed = 7; // 미사일의 속도를 7로 설정합니다.
  var score = 0;

  function drawPlayer() {
    ctx.fillStyle = player.color;
    ctx.fillRect(player.x, player.y, player.width, player.height);
  }

  function drawPlayerBullets() {
    playerBullets.forEach(function(bullet, bulletIndex) {
      ctx.fillStyle = 'blue';
      ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
      bullet.y -= bullet.speed;

      // Remove the bullet if it goes off-screen
      if (bullet.y + bullet.height < 0) {
        playerBullets.splice(bulletIndex, 1);
      }
    });
  }

  function drawMissiles() {
    missiles.forEach(function(missile, index) {
      ctx.fillStyle = 'red';
      ctx.fillRect(missile.x, missile.y, missile.width, missile.height);
      missile.y += missileSpeed;

      // Remove the missile if it goes off-screen
      if (missile.y > canvas.height) {
        missiles.splice(index, 1);
      }
    });
  }

  function collisionDetection() {
    missiles.forEach(function(missile, mIndex) {
      playerBullets.forEach(function(bullet, bIndex) {
        if (bullet.x < missile.x + missile.width &&
            bullet.x + bullet.width > missile.x &&
            bullet.y < missile.y + missile.height &&
            bullet.y + bullet.height > missile.y) {
              missiles.splice(mIndex, 1);
              playerBullets.splice(bIndex, 1);
              score += 10;
            }
      });

      if (missile.x < player.x + player.width &&
          missile.x + missile.width > player.x &&
          missile.y < player.y + player.height &&
          missile.y + missile.height > player.y) {
            alert('게임 오버! 점수: ' + score);
            resetGame();
          }
    });
  }

  function resetGame() {
    player.x = canvas.width / 2;
    player.y = canvas.height - 60;
    playerBullets = [];
    missiles = [];
    score = 0;
    missileSpeed = 7;
  }

  canvas.addEventListener('mousemove', function(event) {
    player.x = event.clientX - canvas.getBoundingClientRect().left - player.width / 2;
  });

  canvas.addEventListener('click', function() {
    playerBullets.push({
      x: player.x + player.width / 2,
      y: player.y,
      width: 5,
      height: 10,
      speed: 10
    });
  });

  function addMissile() {
    missiles.push({
      x: Math.random() * (canvas.width - 20),
      y: -20,
      width: 20,
      height: 50,
      color: '#A00'
    });
  }

  setInterval(addMissile, 2000);

  function gameLoop() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawPlayer();
    drawPlayerBullets();
    drawMissiles();
    collisionDetection();
    if (Math.random() < 0.1) {
      addMissile();
    }
    drawScore();
  }

  function drawScore() {
    ctx.font = "16px Arial";
    ctx.fillStyle = "#000";
    ctx.fillText("점수: " + score, 10, 20);
  }

  setInterval(gameLoop, 1000 / 30);

</script>

</body>
</html>
