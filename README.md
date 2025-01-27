<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>四方块游戏</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      background-color: #f0f0f0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      overflow: hidden;
      font-family: Arial, sans-serif;
    }
    #gameCanvas {
      border: 2px solid black;
      background-color: #fff;
    }
    .game-over {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 36px;
      color: red;
      display: none;
    }
  </style>
</head>
<body>

<canvas id="gameCanvas" width="800" height="600"></canvas>
<div class="game-over" id="gameOverText">恭喜你成功打败了儿子们</div>

<script>
  // 获取游戏画布和上下文
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');

  // 定义方块
  const squares = [
    { x: 100, y: 100, width: 50, height: 50, name: '曹博雄', color: 'blue', alive: true },
    { x: 200, y: 100, width: 50, height: 50, name: '李奕渊', color: 'white', alive: true },
    { x: 300, y: 100, width: 50, height: 50, name: '杨凯尧', color: 'pink', alive: true },
    { x: 400, y: 100, width: 50, height: 50, name: '殷章哲', color: 'black', alive: true }
  ];

  // 定义小球
  const balls = [];
  let isGameOver = false;

  // 控制殷章哲方块的移动
  let player = squares[3];

  // 监听键盘事件
  document.addEventListener('keydown', function (e) {
    if (isGameOver) return;
    
    if (e.key === 'w') player.y -= 5;  // 向上
    if (e.key === 's') player.y += 5;  // 向下
    if (e.key === 'a') player.x -= 5;  // 向左
    if (e.key === 'd') player.x += 5;  // 向右
    if (e.key === ' ') fireBall();    // 发射小球
  });

  // 发射小球
  function fireBall() {
    balls.push({ x: player.x + player.width / 2, y: player.y, radius: 5, color: 'red', speed: 5 });
  }

  // 更新小球位置
  function updateBalls() {
    for (let i = 0; i < balls.length; i++) {
      balls[i].y -= balls[i].speed;  // 小球向上飞
      // 判断小球是否击中方块
      for (let j = 0; j < squares.length; j++) {
        if (squares[j].alive && isCollision(balls[i], squares[j])) {
          squares[j].alive = false;  // 方块消失
          balls.splice(i, 1);  // 小球消失
          i--;  // 调整索引
          checkGameOver();  // 检查游戏是否结束
        }
      }
    }
  }

  // 检查小球和方块是否碰撞
  function isCollision(ball, square) {
    return ball.x > square.x && ball.x < square.x + square.width &&
           ball.y > square.y && ball.y < square.y + square.height;
  }

  // 检查是否游戏结束
  function checkGameOver() {
    if (squares.every(square => !square.alive)) {
      isGameOver = true;
      document.getElementById('gameOverText').style.display = 'block';
    }
  }

  // 绘制方块和小球
  function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);  // 清除画布

    // 绘制方块
    for (let i = 0; i < squares.length; i++) {
      if (squares[i].alive) {
        ctx.fillStyle = squares[i].color;
        ctx.fillRect(squares[i].x, squares[i].y, squares[i].width, squares[i].height);
        ctx.fillStyle = 'black';
        ctx.font = '12px Arial';
        ctx.fillText(squares[i].name, squares[i].x + 5, squares[i].y + 25);
      }
    }

    // 绘制小球
    for (let i = 0; i < balls.length; i++) {
      ctx.fillStyle = balls[i].color;
      ctx.beginPath();
      ctx.arc(balls[i].x, balls[i].y, balls[i].radius, 0, Math.PI * 2);
      ctx.fill();
    }

    // 更新小球的位置
    updateBalls();

    // 如果游戏结束，停止绘制
    if (!isGameOver) {
      requestAnimationFrame(draw);
    }
  }

  // 开始游戏
  draw();
</script>

</body>
</html>
