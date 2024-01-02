<script lang="ts">
    const boardWidth = 10;
    const boardHeight = 10;

    let snake: number[][];
    let direction: [number, number];
    let food: [number, number];
    let intervalID: number | undefined;
    let gameOver = false;
    let score = 0;
    let highScore = getHighScore();

    // interval between each update in [ms]
    let intervalTimeout = 150;

    const HIGH_SCORE_KEY = "highScore";

    function getHighScore() {
        const value = localStorage.getItem(HIGH_SCORE_KEY);
        if (value) {
            const newHighScore = parseInt(value);
            if (newHighScore > boardWidth * boardHeight) {
                return -1;
            }
            return newHighScore;
        }
        return 0;
    }

    function setHighScore() {
        if (score > highScore) {
            localStorage.setItem(HIGH_SCORE_KEY, score.toString());
            highScore = score;
        }
    }

    function setup() {
        score = 0;
        snake = [
            [2, 0],
            [1, 0],
            [0, 0],
        ];
        direction = [1, 0];
        food = pickRandomFreePosition();
        gameOver = false;
    }

    setup();

    function update() {
        const [headX, headY] = snake[0];
        const [directionX, directionY] = direction;

        // calculate next head position
        const [nextX, nextY] = [headX + directionX, headY + directionY];

        if (
            nextX < 0 ||
            nextX >= boardWidth ||
            nextY < 0 ||
            nextY >= boardHeight ||
            snake.find(
                ([snakeX, snakeY]) => snakeX === nextX && snakeY === nextY,
            )
        ) {
            // stop update interval
            clearInterval(intervalID);
            console.log("game over");
            setHighScore();
            return;
        }

        snake.unshift([nextX, nextY]);

        // check if collecting food
        if (nextX === food[0] && nextY === food[1]) {
            score += 1;
            if (snake.length == boardWidth * boardHeight) {
                // stop update interval
                clearInterval(intervalID);
                console.log("you won");
                snake = snake;
                setHighScore();
                return;
            }
            food = pickRandomFreePosition();
        } else {
            // remove tail to keep the same snake length if food was not collected
            snake.pop();
        }

        // update snake to trigger reactivity (rendering)
        snake = snake;
        console.log("update");
    }

    function handleKeydown(e: KeyboardEvent) {
        switch (e.key) {
            case "ArrowUp":
            case "w":
                if (snake[0][1] - snake[1][1] !== 1) direction = [0, -1];
                break;
            case "ArrowDown":
            case "s":
                if (snake[0][1] - snake[1][1] !== -1) direction = [0, 1];
                break;
            case "ArrowLeft":
            case "a":
                if (snake[0][0] - snake[1][0] !== 1) direction = [-1, 0];
                break;
            case "ArrowRight":
            case "d":
                if (snake[0][0] - snake[1][0] !== -1) direction = [1, 0];
                break;
        }
    }

    function pickRandomFreePosition(): [number, number] {
        let randomX: number, randomY: number;
        do {
            [randomX, randomY] = [
                Math.floor(Math.random() * boardWidth),
                Math.floor(Math.random() * boardHeight),
            ];
        } while (
            snake.find(
                ([snakeX, snakeY]) => snakeX === randomX && snakeY === randomY,
            )
        );
        return [randomX, randomY];
    }

    intervalID = setInterval(update, intervalTimeout);
</script>

<!-- globally capture keydown events -->
<svelte:window on:keydown={handleKeydown} />

<main>
    <div class="score">
        Score: {score}
        High score: {highScore}
    </div>

    {#each Array(boardHeight).fill(0) as _, y}
        <div class="row">
            {#each Array(boardWidth).fill(0) as _, x}
                <!-- [{x}, {y}] -->
                <div
                    class="cell
                {snake.find(([sx, sy]) => sx === x && sy === y) ? 'snake' : ''}
                {food[0] === x && food[1] === y ? 'food' : ''} 
                {snake[0][0] === x && snake[0][1] === y ? 'head' : ''} 
                "
                ></div>
            {/each}
        </div>
    {/each}
</main>

<style>
    .row {
        display: flex;
    }

    .cell {
        width: 40px;
        height: 40px;
        border: 2px solid gray;
        border-radius: 15%;
        background-color: greenyellow;
        margin: 1px;
    }

    .snake {
        background-color: slateblue;
    }

    .head {
        background-color: blue;
    }

    .food {
        background-color: red;
    }

    .score {
        font-size: 2rem;
        font-family: Futura, sans-serif;
        font-weight: bold;
        color: #b4b4b4;
    }
</style>