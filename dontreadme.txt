<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Classic Games: Snake, Pong, Pac-Man</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }
        canvas {
            margin-top: 20px;
        }
        #games-container {
            display: none;
            justify-content: space-around;
            width: 100%;
        }
        .game {
            margin: 10px;
            text-align: center;
        }
        #menu-container {
            text-align: center;
        }
        button {
            padding: 10px;
            font-size: 16px;
            margin: 10px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>Classic Games: Snake, Pong, and Pac-Man</h1>

    <!-- Main Menu -->
    <div id="menu-container">
        <button onclick="startGame('snake')">Play Snake</button>
        <button onclick="startGame('pong')">Play Pong</button>
        <button onclick="startGame('pacman')">Play Pac-Man</button>
    </div>

    <!-- Games Container -->
    <div id="games-container">
        <div class="game" id="snake-game">
            <h2>Snake Game</h2>
            <canvas id="snakeCanvas" width="400" height="400"></canvas>
        </div>
        <div class="game" id="pong-game">
            <h2>Pong Game</h2>
            <canvas id="pongCanvas" width="400" height="400"></canvas>
        </div>
        <div class="game" id="pacman-game">
            <h2>Pac-Man Game</h2>
            <canvas id="pacmanCanvas" width="400" height="400"></canvas>
        </div>
    </div>

    <script>
        // Snake Game
        const snakeCanvas = document.getElementById("snakeCanvas");
        const snakeCtx = snakeCanvas.getContext("2d");

        const snakeGame = {
            gridSize: 20,
            gridCount: 20,
            snake: [{ x: 9, y: 9 }],
            direction: { x: 0, y: 0 },
            food: { x: 15, y: 15 },
            isGameOver: false,

            update: function () {
                if (this.isGameOver) return;

                let head = { ...this.snake[0] };
                head.x += this.direction.x;
                head.y += this.direction.y;

                if (
                    head.x < 0 ||
                    head.x >= this.gridCount ||
                    head.y < 0 ||
                    head.y >= this.gridCount ||
                    this.snake.some(segment => segment.x === head.x && segment.y === head.y)
                ) {
                    this.isGameOver = true;
                    alert("Game Over! Snake hit the wall or itself!");
                    return;
                }

                this.snake.unshift(head);

                if (head.x === this.food.x && head.y === this.food.y) {
                    this.food = {
                        x: Math.floor(Math.random() * this.gridCount),
                        y: Math.floor(Math.random() * this.gridCount)
                    };
                } else {
                    this.snake.pop();
                }

                this.draw();
            },

            draw: function () {
                snakeCtx.clearRect(0, 0, snakeCanvas.width, snakeCanvas.height);
                this.snake.forEach(segment => {
                    snakeCtx.fillStyle = "#00ff00";
                    snakeCtx.fillRect(segment.x * this.gridSize, segment.y * this.gridSize, this.gridSize, this.gridSize);
                });
                snakeCtx.fillStyle = "#ff0000";
                snakeCtx.fillRect(this.food.x * this.gridSize, this.food.y * this.gridSize, this.gridSize, this.gridSize);
            },

            changeDirection: function (event) {
                if (event.key === "ArrowUp" && this.direction.y === 0) {
                    this.direction = { x: 0, y: -1 };
                } else if (event.key === "ArrowDown" && this.direction.y === 0) {
                    this.direction = { x: 0, y: 1 };
                } else if (event.key === "ArrowLeft" && this.direction.x === 0) {
                    this.direction = { x: -1, y: 0 };
                } else if (event.key === "ArrowRight" && this.direction.x === 0) {
                    this.direction = { x: 1, y: 0 };
                }
            },

            reset: function () {
                this.snake = [{ x: 9, y: 9 }];
                this.direction = { x: 0, y: 0 };
                this.food = { x: 15, y: 15 };
                this.isGameOver = false;
            }
        };

        // Pong Game
        const pongCanvas = document.getElementById("pongCanvas");
        const pongCtx = pongCanvas.getContext("2d");

        const pongGame = {
            paddleHeight: 60,
            paddleWidth: 10,
            ballSize: 10,
            leftPaddle: { x: 10, y: 150, dy: 0 },
            rightPaddle: { x: 380, y: 150, dy: 0 },
            ball: { x: 200, y: 200, dx: 2, dy: 2 },
            isGameOver: false,

            update: function () {
                if (this.isGameOver) return;

                this.ball.x += this.ball.dx;
                this.ball.y += this.ball.dy;

                if (this.ball.y <= 0 || this.ball.y >= pongCanvas.height - this.ballSize) {
                    this.ball.dy *= -1;
                }

                if (this.ball.x <= this.leftPaddle.x + this.paddleWidth && this.ball.y >= this.leftPaddle.y && this.ball.y <= this.leftPaddle.y + this.paddleHeight) {
                    this.ball.dx *= -1;
                } else if (this.ball.x >= this.rightPaddle.x - this.ballSize && this.ball.y >= this.rightPaddle.y && this.ball.y <= this.rightPaddle.y + this.paddleHeight) {
                    this.ball.dx *= -1;
                }

                if (this.ball.x <= 0 || this.ball.x >= pongCanvas.width - this.ballSize) {
                    this.isGameOver = true;
                    alert("Game Over! Ball went out of bounds.");
                }

                this.leftPaddle.y += this.leftPaddle.dy;
                this.rightPaddle.y += this.rightPaddle.dy;

                if (this.leftPaddle.y < 0) this.leftPaddle.y = 0;
                if (this.leftPaddle.y > pongCanvas.height - this.paddleHeight) this.leftPaddle.y = pongCanvas.height - this.paddleHeight;

                if (this.rightPaddle.y < 0) this.rightPaddle.y = 0;
                if (this.rightPaddle.y > pongCanvas.height - this.paddleHeight) this.rightPaddle.y = pongCanvas.height - this.paddleHeight;

                this.draw();
            },

            draw: function () {
                pongCtx.clearRect(0, 0, pongCanvas.width, pongCanvas.height);
                pongCtx.fillStyle = "#00ff00";
                pongCtx.fillRect(this.leftPaddle.x, this.leftPaddle.y, this.paddleWidth, this.paddleHeight);
                pongCtx.fillRect(this.rightPaddle.x, this.rightPaddle.y, this.paddleWidth, this.paddleHeight);
                pongCtx.fillStyle = "#ff0000";
                pongCtx.fillRect(this.ball.x, this.ball.y, this.ballSize, this.ballSize);
            },

            changeDirection: function (event) {
                if (event.key === "ArrowUp") {
                    this.rightPaddle.dy = -5;
                } else if (event.key === "ArrowDown") {
                    this.rightPaddle.dy = 5;
                }

                if (event.key === "w") {
                    this.leftPaddle.dy = -5;
                } else if (event.key === "s") {
                    this.leftPaddle.dy = 5;
                }
            },

            reset: function () {
                this.leftPaddle.y = 150;
                this.rightPaddle.y = 150;
                this.ball.x = 200;
                this.ball.y = 200;
                this.ball.dx = 2;
                this.ball.dy = 2;
                this.isGameOver = false;
            }
        };

        // Pac-Man Game - Basic Implementation
        const pacmanCanvas = document.getElementById("pacmanCanvas");
        const pacmanCtx = pacmanCanvas.getContext("2d");

        const pacmanGame = {
            pacman: { x: 200, y: 200, radius: 20, direction: 0 },
            update: function () {
                this.pacman.x += Math.cos(this.pacman.direction) * 5;
                this.pacman.y += Math.sin(this.pacman.direction) * 5;

                if (this.pacman.x <= 0 || this.pacman.x >= pacmanCanvas.width || this.pacman.y <= 0 || this.pacman.y >= pacmanCanvas.height) {
                    alert("Pac-Man hit the wall!");
                    this.pacman.x = 200;
                    this.pacman.y = 200;
                }

                this.draw();
            },

            draw: function () {
                pacmanCtx.clearRect(0, 0, pacmanCanvas.width, pacmanCanvas.height);
                pacmanCtx.beginPath();
                pacmanCtx.arc(this.pacman.x, this.pacman.y, this.pacman.radius, 0.2 * Math.PI, 1.8 * Math.PI);
                pacmanCtx.lineTo(this.pacman.x, this.pacman.y);
                pacmanCtx.fillStyle = "#FFFF00";
                pacmanCtx.fill();
            },

            changeDirection: function (event) {
                if (event.key === "ArrowUp") {
                    this.pacman.direction = -Math.PI / 2;
                } else if (event.key === "ArrowDown") {
                    this.pacman.direction = Math.PI / 2;
                } else if (event.key === "ArrowLeft") {
                    this.pacman.direction = Math.PI;
                } else if (event.key === "ArrowRight") {
                    this.pacman.direction = 0;
                }
            }
        };

        // Show and hide games based on user selection
        function startGame(game) {
            // Hide menu
            document.getElementById('menu-container').style.display = 'none';
            // Show games container
            document.getElementById('games-container').style.display = 'flex';

            // Hide all games
            document.getElementById('snake-game').style.display = 'none';
            document.getElementById('pong-game').style.display = 'none';
            document.getElementById('pacman-game').style.display = 'none';

            // Show the selected game
            if (game === 'snake') {
                document.getElementById('snake-game').style.display = 'block';
                window.addEventListener("keydown", (event) => snakeGame.changeDirection(event));
                setInterval(() => snakeGame.update(), 100);
            } else if (game === 'pong') {
                document.getElementById('pong-game').style.display = 'block';
                window.addEventListener("keydown", (event) => pongGame.changeDirection(event));
                setInterval(() => pongGame.update(), 1000 / 60);
            } else if (game === 'pacman') {
                document.getElementById('pacman-game').style.display = 'block';
                window.addEventListener("keydown", (event) => pacmanGame.changeDirection(event));
                setInterval(() => pacmanGame.update(), 1000 / 60);
            }
        }
    </script>
</body>
</html>
