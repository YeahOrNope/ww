<script lang="ts">
	const boardwidth = 10;
    const boardWeight = 10;

    let snake: rumber[][];
    let direction: [number, number];

    let intervalID: number | undefined;



    let intervalTimeout = 150;

    function setup() {
        snake = [
            [2,0],
            [1,0],
            [0,0],
        ];

    }

setup();

    funtion uptade() {
        const [headX, headY] = snake[0];
        const [headX + directionX, headY + directionY];

        snake.unshift([nextX, nextY]);

        snake.pop();

        snake = snake;
        console.log("uptade");
    }

    function handleKeydown(e: KeyboardEvent){
        switch (e.key) {
            case "ArrowUp":
            case "w":
                if (snake[0][1] - snake[1][1] != 1) direction = [0,-1];
                break;
                case "ArrowDown":
                case "s":
                    if (snake[0][1] - snake[1][1] != 1) direction = [0,1];
                    break;
                case "ArrowLeft":
                case "a":
                    if (snake[0][1] - snake[1][1] != 1) direction = [-1,0];
                    break;
                case "ArrowRight":
                case "d":
                    if (snake[0][1] - snake[1][1] != 1) direction = [1,0];
                    break;
        }
    }

    setInterval(uptade, internalTimeout);
</script>

<main>
    {#each Array (boardHeight).fill(0) as _,y}
        <div class="row">
            {#each Array(boardWidth).fill(0) as _,x}
                <div class="cell {snake}.find([sx, sy])=> sx === x && sy === y) ? 'snake' : '' }">

                </div>
        </div>
</main>

<style>
    /* Styl strony */
</style>
