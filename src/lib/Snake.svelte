<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	// 游戏配置
	const GRID_SIZE = 20;
	const CELL_SIZE = 20;
	const INITIAL_SPEED = 150;
	const SPEED_INCREMENT = 10;
	const MIN_SPEED = 50;

	// 方向常量
	const Direction = {
		UP: 'UP',
		DOWN: 'DOWN',
		LEFT: 'LEFT',
		RIGHT: 'RIGHT'
	} as const;
	
	type DirectionType = typeof Direction[keyof typeof Direction];

	// 位置接口
	interface Position {
		x: number;
		y: number;
	}

	// 游戏状态
	let snake: Position[] = [{ x: 10, y: 10 }];
	let direction: DirectionType = Direction.RIGHT;
	let nextDirection: DirectionType = Direction.RIGHT;
	let food: Position = generateFood();
	let gameRunning = false;
	let gameOver = false;
	let score = 0;
	let highScore = 0;
	let speed = INITIAL_SPEED;
	let intervalId: number | null = null;

	// 生成食物位置
	function generateFood(): Position {
		let newFood: Position;
		do {
			newFood = {
				x: Math.floor(Math.random() * GRID_SIZE),
				y: Math.floor(Math.random() * GRID_SIZE)
			};
		} while (snake.some(segment => segment.x === newFood.x && segment.y === newFood.y));
		return newFood;
	}

	// 检查碰撞
	function checkCollision(head: Position): boolean {
		// 检查墙壁碰撞
		if (head.x < 0 || head.x >= GRID_SIZE || head.y < 0 || head.y >= GRID_SIZE) {
			return true;
		}
		// 检查自身碰撞
		return snake.slice(1).some(segment => segment.x === head.x && segment.y === head.y);
	}

	// 移动蛇
	function moveSnake() {
		if (!gameRunning || gameOver) return;

		const head = { ...snake[0] };
		direction = nextDirection;

		// 根据方向移动头部
		switch (direction) {
			case Direction.UP:
				head.y -= 1;
				break;
			case Direction.DOWN:
				head.y += 1;
				break;
			case Direction.LEFT:
				head.x -= 1;
				break;
			case Direction.RIGHT:
				head.x += 1;
				break;
		}

		// 检查碰撞
		if (checkCollision(head)) {
			endGame();
			return;
		}

		// 将新头部加入蛇身
		snake = [head, ...snake];

		// 检查是否吃到食物
		if (head.x === food.x && head.y === food.y) {
			score += 10;
			if (score > highScore) {
				highScore = score;
				localStorage.setItem('snakeHighScore', highScore.toString());
			}
			food = generateFood();
			// 增加游戏速度
			if (speed > MIN_SPEED) {
				speed -= SPEED_INCREMENT;
				if (intervalId !== null) {
					clearInterval(intervalId);
					intervalId = setInterval(moveSnake, speed);
				}
			}
		} else {
			// 如果没吃到食物，移除尾部
			snake = snake.slice(0, -1);
		}
	}

	// 处理键盘输入
	function handleKeyPress(event: KeyboardEvent) {
		if (!gameRunning || gameOver) {
			if (event.key === ' ') {
				startGame();
			}
			return;
		}

		const key = event.key.toLowerCase();
		switch (key) {
			case 'arrowup':
			case 'w':
				if (direction !== Direction.DOWN) {
					nextDirection = Direction.UP;
				}
				break;
			case 'arrowdown':
			case 's':
				if (direction !== Direction.UP) {
					nextDirection = Direction.DOWN;
				}
				break;
			case 'arrowleft':
			case 'a':
				if (direction !== Direction.RIGHT) {
					nextDirection = Direction.LEFT;
				}
				break;
			case 'arrowright':
			case 'd':
				if (direction !== Direction.LEFT) {
					nextDirection = Direction.RIGHT;
				}
				break;
			case ' ':
				pauseGame();
				break;
		}
	}

	// 开始游戏
	function startGame() {
		snake = [{ x: 10, y: 10 }];
		direction = Direction.RIGHT;
		nextDirection = Direction.RIGHT;
		food = generateFood();
		score = 0;
		speed = INITIAL_SPEED;
		gameRunning = true;
		gameOver = false;
		if (intervalId !== null) {
			clearInterval(intervalId);
		}
		intervalId = setInterval(moveSnake, speed);
	}

	// 暂停游戏
	function pauseGame() {
		if (!gameOver) {
			gameRunning = !gameRunning;
			if (!gameRunning && intervalId !== null) {
				clearInterval(intervalId);
				intervalId = null;
			} else if (gameRunning) {
				intervalId = setInterval(moveSnake, speed);
			}
		}
	}

	// 结束游戏
	function endGame() {
		gameRunning = false;
		gameOver = true;
		if (intervalId !== null) {
			clearInterval(intervalId);
			intervalId = null;
		}
	}

	// 组件挂载时
	onMount(() => {
		// 从本地存储加载最高分
		const savedHighScore = localStorage.getItem('snakeHighScore');
		if (savedHighScore) {
			highScore = parseInt(savedHighScore);
		}
		
		// 添加键盘事件监听
		if (typeof window !== 'undefined') {
			window.addEventListener('keydown', handleKeyPress);
		}
	});

	// 组件销毁时
	onDestroy(() => {
		if (intervalId !== null) {
			clearInterval(intervalId);
		}
		if (typeof window !== 'undefined') {
			window.removeEventListener('keydown', handleKeyPress);
		}
	});
</script>

<div class="game-container">
	<div class="info-panel">
		<h1>🐍 贪吃蛇游戏</h1>
		<div class="scores">
			<div class="score">
				<span>当前分数:</span>
				<span class="score-value">{score}</span>
			</div>
			<div class="score">
				<span>最高分数:</span>
				<span class="score-value">{highScore}</span>
			</div>
		</div>
		<div class="controls">
			<p>使用 WASD 或方向键控制</p>
			<p>按空格键 {gameRunning ? '暂停' : gameOver ? '重新开始' : '开始游戏'}</p>
		</div>
		{#if !gameRunning && !gameOver}
			<button on:click={startGame} class="start-button">开始游戏</button>
		{:else if gameOver}
			<div class="game-over">
				<h2>游戏结束！</h2>
				<button on:click={startGame} class="start-button">再玩一次</button>
			</div>
		{:else if !gameRunning}
			<div class="paused">
				<h2>游戏暂停</h2>
				<button on:click={pauseGame} class="start-button">继续游戏</button>
			</div>
		{/if}
	</div>

	<div class="game-board" style="width: {GRID_SIZE * CELL_SIZE}px; height: {GRID_SIZE * CELL_SIZE}px;">
		<!-- 渲染蛇 -->
		{#each snake as segment, i}
			<div 
				class="snake-segment {i === 0 ? 'snake-head' : ''}"
				style="
					left: {segment.x * CELL_SIZE}px;
					top: {segment.y * CELL_SIZE}px;
					width: {CELL_SIZE}px;
					height: {CELL_SIZE}px;
				"
			></div>
		{/each}
		
		<!-- 渲染食物 -->
		<div 
			class="food"
			style="
				left: {food.x * CELL_SIZE}px;
				top: {food.y * CELL_SIZE}px;
				width: {CELL_SIZE}px;
				height: {CELL_SIZE}px;
			"
			></div>
	</div>
</div>

<style>
	.game-container {
		display: flex;
		gap: 2rem;
		padding: 2rem;
		max-width: 1000px;
		margin: 0 auto;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
	}

	.info-panel {
		flex: 1;
		min-width: 250px;
	}

	.info-panel h1 {
		margin: 0 0 1.5rem 0;
		font-size: 2rem;
		color: #333;
	}

	.scores {
		margin-bottom: 1.5rem;
	}

	.score {
		display: flex;
		justify-content: space-between;
		margin-bottom: 0.5rem;
		font-size: 1.1rem;
	}

	.score-value {
		font-weight: bold;
		color: #4CAF50;
	}

	.controls {
		margin-bottom: 1.5rem;
		color: #666;
	}

	.controls p {
		margin: 0.5rem 0;
	}

	.start-button {
		background-color: #4CAF50;
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		font-size: 1rem;
		border-radius: 5px;
		cursor: pointer;
		transition: background-color 0.3s;
	}

	.start-button:hover {
		background-color: #45a049;
	}

	.game-over, .paused {
		margin-top: 1rem;
	}

	.game-over h2, .paused h2 {
		color: #f44336;
		margin-bottom: 1rem;
	}

	.paused h2 {
		color: #ff9800;
	}

	.game-board {
		position: relative;
		background-color: #f0f0f0;
		border: 2px solid #333;
		box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
	}

	.snake-segment {
		position: absolute;
		background-color: #4CAF50;
		border-radius: 3px;
		transition: all 0.1s;
	}

	.snake-head {
		background-color: #2E7D32;
		z-index: 10;
	}

	.food {
		position: absolute;
		background-color: #f44336;
		border-radius: 50%;
		animation: pulse 0.8s infinite;
	}

	@keyframes pulse {
		0% {
			transform: scale(1);
		}
		50% {
			transform: scale(1.1);
		}
		100% {
			transform: scale(1);
		}
	}

	@media (max-width: 768px) {
		.game-container {
			flex-direction: column;
			align-items: center;
		}

		.info-panel {
			text-align: center;
			margin-bottom: 1rem;
		}
	}
</style>
