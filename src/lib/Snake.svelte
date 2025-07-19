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
	let gamePaused = false;
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
		// 阻止空格键的默认行为（滚动页面）
		if (event.key === ' ') {
			event.preventDefault();
		}
		
		// 如果游戏结束或者尚未开始，空格键开始游戏
		if ((!gameRunning && !gamePaused) || gameOver) {
			if (event.key === ' ') {
				startGame();
			}
			return;
		}

		// 如果游戏暂停，空格键恢复游戏
		if (gamePaused) {
			if (event.key === ' ') {
				pauseGame();
			}
			return;
		}

		// 游戏运行中的键盘控制
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
		gamePaused = false;
		if (intervalId !== null) {
			clearInterval(intervalId);
		}
		intervalId = setInterval(moveSnake, speed);
	}

	// 暂停游戏
	function pauseGame() {
		if (!gameOver) {
			gamePaused = !gamePaused;
			if (gamePaused && intervalId !== null) {
				clearInterval(intervalId);
				intervalId = null;
				gameRunning = false;
			} else if (!gamePaused) {
				gameRunning = true;
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
			<p>按空格键 {gameRunning && !gamePaused ? '暂停' : gameOver ? '重新开始' : '开始游戏'}</p>
		</div>
		{#if !gameRunning && !gameOver && !gamePaused}
			<button on:click={startGame} class="start-button">开始游戏</button>
		{:else if gameOver}
			<div class="game-over">
				<h2>游戏结束！</h2>
				<button on:click={startGame} class="start-button">再玩一次</button>
			</div>
		{:else if gamePaused}
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
		gap: 3rem;
		padding: 2rem;
		max-width: 1100px;
		margin: 0 auto;
		background: rgba(255, 255, 255, 0.95);
		border-radius: 20px;
		box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
		backdrop-filter: blur(10px);
	}

	.info-panel {
		flex: 1;
		min-width: 280px;
		padding: 1rem;
	}

	.info-panel h1 {
		margin: 0 0 2rem 0;
		font-size: 2.5rem;
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		background-clip: text;
		text-align: center;
		font-weight: 800;
		text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
	}

	.scores {
		margin-bottom: 2rem;
		background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
		padding: 1.5rem;
		border-radius: 15px;
		box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
	}

	.score {
		display: flex;
		justify-content: space-between;
		margin-bottom: 1rem;
		font-size: 1.2rem;
		color: #2c3e50;
	}

	.score:last-child {
		margin-bottom: 0;
	}

	.score-value {
		font-weight: 700;
		font-size: 1.4rem;
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		background-clip: text;
	}

	.controls {
		margin-bottom: 2rem;
		padding: 1rem;
		background: rgba(0, 0, 0, 0.05);
		border-radius: 10px;
	}

	.controls p {
		margin: 0.5rem 0;
		color: #4a5568;
		font-size: 0.95rem;
		display: flex;
		align-items: center;
		gap: 0.5rem;
	}

	.controls p::before {
		content: '▸';
		color: #667eea;
		font-weight: bold;
	}

	.start-button {
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		color: white;
		border: none;
		padding: 1rem 2rem;
		font-size: 1.1rem;
		font-weight: 600;
		border-radius: 50px;
		cursor: pointer;
		transition: all 0.3s ease;
		box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
		text-transform: uppercase;
		letter-spacing: 1px;
	}

	.start-button:hover {
		transform: translateY(-2px);
		box-shadow: 0 6px 20px rgba(102, 126, 234, 0.6);
	}

	.start-button:active {
		transform: translateY(0);
	}

	.game-over, .paused {
		margin-top: 1.5rem;
		padding: 1.5rem;
		background: rgba(255, 255, 255, 0.9);
		border-radius: 15px;
		box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
		text-align: center;
	}

	.game-over h2, .paused h2 {
		margin-bottom: 1rem;
		font-size: 1.8rem;
	}

	.game-over h2 {
		background: linear-gradient(135deg, #ff512f 0%, #dd2476 100%);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		background-clip: text;
	}

	.paused h2 {
		background: linear-gradient(135deg, #f7971e 0%, #ffd200 100%);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		background-clip: text;
	}

	.game-board {
		position: relative;
		background: linear-gradient(to bottom right, #1a1a2e, #16213e);
		border: none;
		border-radius: 15px;
		box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3), inset 0 1px 0 rgba(255, 255, 255, 0.1);
		overflow: hidden;
	}

	.game-board::before {
		content: '';
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-image: 
			linear-gradient(rgba(255, 255, 255, 0.03) 1px, transparent 1px),
			linear-gradient(to right, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
		background-size: 20px 20px;
		pointer-events: none;
	}

	.snake-segment {
		position: absolute;
		background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%);
		border-radius: 4px;
		transition: all 0.1s ease;
		box-shadow: 0 2px 8px rgba(17, 153, 142, 0.4);
	}

	.snake-segment::after {
		content: '';
		position: absolute;
		top: 2px;
		left: 2px;
		right: 2px;
		bottom: 2px;
		background: rgba(255, 255, 255, 0.2);
		border-radius: 2px;
	}

	.snake-head {
		background: linear-gradient(135deg, #0f7938 0%, #11998e 100%);
		z-index: 10;
		box-shadow: 0 4px 12px rgba(15, 121, 56, 0.6);
	}

	.snake-head::before {
		content: '';
		position: absolute;
		top: 4px;
		width: 4px;
		height: 4px;
		background: #fff;
		border-radius: 50%;
		box-shadow: 0 0 3px rgba(255, 255, 255, 0.8);
	}

	.snake-head::before {
		left: 4px;
	}

	.snake-head::after {
		content: '';
		position: absolute;
		top: 4px;
		right: 4px;
		width: 4px;
		height: 4px;
		background: #fff;
		border-radius: 50%;
		box-shadow: 0 0 3px rgba(255, 255, 255, 0.8);
	}

	.food {
		position: absolute;
		background: linear-gradient(135deg, #fc466b 0%, #3f5efb 100%);
		border-radius: 50%;
		animation: pulse 0.8s infinite, rotate 4s linear infinite;
		box-shadow: 0 4px 15px rgba(252, 70, 107, 0.6);
	}

	.food::after {
		content: '';
		position: absolute;
		top: 25%;
		left: 25%;
		width: 50%;
		height: 50%;
		background: rgba(255, 255, 255, 0.3);
		border-radius: 50%;
		filter: blur(4px);
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

	@keyframes rotate {
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(360deg);
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
