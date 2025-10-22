# toptutma.github.io
Basit top tutma oyunu
<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<title>Top Oyunu</title>
<style>
    body {
        display: flex;
        flex-direction: column;
        align-items: center;
        font-family: Arial, sans-serif;
        background-color: #f0f0f0;
        margin: 0;
        padding: 0;
    }
    canvas {
        background-color: #222;
        display: block;
        margin-top: 20px;
        border: 2px solid #000;
    }
    button {
        margin-top: 20px;
        padding: 10px 20px;
        font-size: 16px;
        cursor: pointer;
    }
</style>
</head>
<body>

<h1>Top Oyunu</h1>
<button id="startBtn">Oyunu Başlat</button>
<canvas id="gameCanvas" width="500" height="400"></canvas>
<div id="scoreDisplay" style="margin-top: 20px; font-size: 18px;"></div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const startBtn = document.getElementById('startBtn');
const scoreDisplay = document.getElementById('scoreDisplay');

let paddle, ball, score, animationId;
let gameRunning = false;

function resetGame() {
    paddle = {
        width: 100,
        height: 10,
        x: canvas.width/2 - 50,
        y: canvas.height - 20,
        speed: 8,
        dx: 0
    };

    ball = {
        x: canvas.width/2,
        y: canvas.height/2,
        radius: 10,
        speed: 4,
        dx: 4,
        dy: -4
    };

    score = 0;
    scoreDisplay.textContent = '';
    gameRunning = true;
    animate();
}

function drawPaddle() {
    ctx.fillStyle = '#00ff00';
    ctx.fillRect(paddle.x, paddle.y, paddle.width, paddle.height);
}

function drawBall() {
    ctx.beginPath();
    ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
    ctx.fillStyle = '#ff0000';
    ctx.fill();
    ctx.closePath();
}

function movePaddle() {
    paddle.x += paddle.dx;

    // Duvarlara çarpmasını önle
    if(paddle.x < 0) paddle.x = 0;
    if(paddle.x + paddle.width > canvas.width) paddle.x = canvas.width - paddle.width;
}

function moveBall() {
    ball.x += ball.dx;
    ball.y += ball.dy;

    // Duvarlara çarpma
    if(ball.x + ball.radius > canvas.width || ball.x - ball.radius < 0) {
        ball.dx *= -1;
    }
    if(ball.y - ball.radius < 0) {
        ball.dy *= -1;
    }

    // Paddle ile çarpma
    if(ball.y + ball.radius >= paddle.y &&
       ball.x >= paddle.x &&
       ball.x <= paddle.x + paddle.width) {
        ball.dy *= -1;
        ball.speed += 0.5;
        if(ball.dx > 0) ball.dx += 0.5; else ball.dx -= 0.5;
        score++;
    }

    // Altı geçtiyse oyun biter
    if(ball.y - ball.radius > canvas.height) {
        gameOver();
    }
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawPaddle();
    drawBall();
}

function update() {
    movePaddle();
    moveBall();
    draw();
}

function animate() {
    if(!gameRunning) return;
    update();
    animationId = requestAnimationFrame(animate);
}

function gameOver() {
    gameRunning = false;
    cancelAnimationFrame(animationId);
    scoreDisplay.textContent = `Oyun Bitti! Top ${score} kez sekmiş.`;
    startBtn.textContent = 'Tekrar Başlat';
    startBtn.style.display = 'block';
}

// Paddle kontrolü
document.addEventListener('keydown', e => {
    if(e.key === 'ArrowRight') paddle.dx = paddle.speed;
    else if(e.key === 'ArrowLeft') paddle.dx = -paddle.speed;
});
document.addEventListener('keyup', e => {
    if(e.key === 'ArrowRight' || e.key === 'ArrowLeft') paddle.dx = 0;
});

// Start Button
startBtn.addEventListener('click', () => {
    startBtn.style.display = 'none';
    resetGame();
});
</script>

</body>
</html>
