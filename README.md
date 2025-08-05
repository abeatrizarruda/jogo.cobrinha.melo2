# jogo.cobrinha.melo2
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Jogo da Cobrinha</title>
  <style>
    body {
      background-color: #111;
      color: white;
      font-family: Arial, sans-serif;
      text-align: center;
    }
    canvas {
      background-color: #000;
      display: block;
      margin: 20px auto;
      border: 2px solid #fff;
    }
    #game-over {
      display: none;
    }
  </style>
</head>
<body>
  <h1>Jogo da Cobrinha</h1>
  <canvas id="gameCanvas" width="400" height="400"></canvas>
  <div id="game-over">
    <h2>Você perdeu!</h2>
    <button onclick="restartGame()">Recomeçar</button>
  </div>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const box = 20;
    const canvasSize = 400;
    let snake = [{ x: 200, y: 200 }];
    let direction = 'RIGHT';
    let food = generateFood();
    let snakeColor = getRandomColor();
    let game;

    document.addEventListener('keydown', changeDirection);

    function getRandomColor() {
      const letters = '0123456789ABCDEF';
      let color = '#';
      for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    }

    function generateFood() {
      return {
        x: Math.floor(Math.random() * (canvasSize / box)) * box,
        y: Math.floor(Math.random() * (canvasSize / box)) * box
      };
    }

    function changeDirection(event) {
      const key = event.keyCode;
      if (key === 37 && direction !== 'RIGHT') direction = 'LEFT';
      else if (key === 38 && direction !== 'DOWN') direction = 'UP';
      else if (key === 39 && direction !== 'LEFT') direction = 'RIGHT';
      else if (key === 40 && direction !== 'UP') direction = 'DOWN';
    }

    function drawGame() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // desenha a cobrinha
      for (let i = 0; i < snake.length; i++) {
        ctx.fillStyle = snakeColor;
        ctx.fillRect(snake[i].x, snake[i].y, box, box);
      }

      // desenha a comida
      ctx.fillStyle = 'red';
      ctx.fillRect(food.x, food.y, box, box);

      let head = { ...snake[0] };

      if (direction === 'LEFT') head.x -= box;
      if (direction === 'UP') head.y -= box;
      if (direction === 'RIGHT') head.x += box;
      if (direction === 'DOWN') head.y += box;

      // verifica colisão com bordas
      if (head.x < 0 || head.y < 0 || head.x >= canvasSize || head.y >= canvasSize) {
        endGame();
        return;
      }

      // verifica colisão com corpo
      for (let i = 1; i < snake.length; i++) {
        if (head.x === snake[i].x && head.y === snake[i].y) {
          endGame();
          return;
        }
      }

      snake.unshift(head);

      // verifica se comeu a comida
      if (head.x === food.x && head.y === food.y) {
        food = generateFood();
        snakeColor = getRandomColor();
      } else {
        snake.pop();
      }
    }

    function endGame() {
      clearInterval(game);
      document.getElementById('game-over').style.display = 'block';
    }

    function restartGame() {
      snake = [{ x: 200, y: 200 }];
      direction = 'RIGHT';
      food = generateFood();
      snakeColor = getRandomColor();
      document.getElementById('game-over').style.display = 'none';
      game = setInterval(drawGame, 100);
    }

    game = setInterval(drawGame, 100);
  </script>
</body>
</html>

