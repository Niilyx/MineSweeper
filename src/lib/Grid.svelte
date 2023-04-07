<script>
    const Game = class {
        constructor(minesTotal, w = 10, h = 5) {
            this.grid = [];
            this.gridW = w;
            this.gridH = h;
            this.minesTotal = minesTotal;
            if (this.minesTotal > this.getWidth() * this.getHeight()) {
                throw new EvalError("Too many mines for the grid size");
            }

            this.initGrid();
            this.placeMines();

            this.gameOver = 0; // 0: not over, 1: won, -1: lost
        }

        getWidth() {
            return this.gridW;
        }

        getHeight() {
            return this.gridH;
        }

        getGrid() {
            return this.grid;
        }

        initGrid() {
            for (let i = 0; i < this.getHeight(); i++) {
                const line = [];
                for (let j = 0; j < this.getWidth(); j++) {
                    line.push(new Cell());
                }
                this.grid.push(line);
            }
        };

        placeMines() {
            let minesToPlace = this.minesTotal;
            while (minesToPlace > 0) {
                const x = Math.floor(Math.random() * this.getWidth());
                const y = Math.floor(Math.random() * this.getHeight());
                if (!this.grid[y][x].mine) {
                    this.grid[y][x].mine = true;
                    minesToPlace--;
                }
            }
        }
        setGameOver(result) {
            this.gameOver = result;
            for (let i = 0; i < this.getHeight(); i++) {
                for (let j = 0; j < this.getWidth(); j++) {
                    this.grid[i][j].state = Cell.CELL_STATES.REVEALED;
                }
            }
        }

        cellLeftClicked(x, y) {
            if (this.grid[y][x].state === Cell.CELL_STATES.FLAGGED) {
                return;
            }
            if (this.grid[y][x].mine) {
                this.setGameOver(-1);
                return;
            }
            if (this.grid[y][x].state === Cell.CELL_STATES.UNCLICKED) {
                this.grid[y][x].state = Cell.CELL_STATES.REVEALED;

                const surroundingMines = this.getSurroundingMines(x, y);
                this.grid[y][x].value = new String(surroundingMines === 0 ? "" : `./src/assets/numbers/${surroundingMines}.png`);
            }
        }
        getSurroundingMines(x, y) {
            let mines = 0;
            for (let i = -1; i <= 1; i++) {
                for (let j = -1; j <= 1; j++) {
                    if (i === 0 && j === 0) {
                        continue;
                    }
                    if (this.grid[y + i] && this.grid[y + i][x + j]) {
                        if (this.grid[y + i][x + j].mine) {
                            mines++;
                        }
                    }
                }
            }
            return mines;
        }
        getCellImage(x, y) {
            if (this.grid[y][x].state === Cell.CELL_STATES.UNCLICKED) return "";
            return this.grid[y][x].mine ? "./src/assets/mine.png" : this.grid[y][x].value;
        }
    };

    const Cell = class {
        constructor() {
            this.state = Cell.CELL_STATES.UNCLICKED;
            this.mine = false;
            this.value = "";
        }
        nextState() {
            switch (this.state) {
                case Cell.CELL_STATES.UNCLICKED:
                    this.state = Cell.CELL_STATES.FLAGGED;
                    break;
                case Cell.CELL_STATES.FLAGGED:
                    this.state = Cell.CELL_STATES.DOUBT;
                    break;
                case Cell.CELL_STATES.DOUBT:
                    this.state = Cell.CELL_STATES.UNCLICKED;
                    break;
            }
        }
    };

    Cell.CELL_STATES = {
        UNCLICKED: "unclicked",
        REVEALED: "revealed",
        FLAGGED: "flagged",
        DOUBT: "doubt",
    };

    let gameInstance;
    let mainElement;
    
    export const newGame = () => {
        try {
            gameInstance = new Game(10);
        } catch (e) {
            console.error(e);
        }
    };

    const onCellClick = (event) => {
        /*
        0: left click
        1: middle click
        2: right click
        */
        const clickType = event.button;

        let [x, y] = event.target.id.split(";");
        x = parseInt(x);
        y = parseInt(y);
        switch (clickType) {
            case 0:
                gameInstance.cellLeftClicked(x, y);
                break;
            case 2:
                gameInstance.getGrid()[y][x].nextState();
                break;
        }
        gameInstance = gameInstance; // svelte quirks
    };
</script>





<grid bind:this={mainElement} on:contextmenu|preventDefault={() => {return false;}}>
    {#if gameInstance}
        <wrapper>
            {#each gameInstance.getGrid() as line, y}
                <div class="line">
                    {#each line as cell, x}
                        <div id="{x};{y}" class="cell {cell.state}"
                        on:mouseup|preventDefault|stopPropagation={onCellClick}
                        ><img src={gameInstance.getCellImage(x, y)} alt={cell.value.at(cell.value.lastIndexOf(".") - 1)}/></div>
                    {/each}
                </div>
            {/each}
        </wrapper>
    {/if}
</grid>





<style>
    grid {
        margin: 0 auto;
        width: 80%;

        justify-content: center;
        align-content: center;
    }
    wrapper {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-content: center;
    }
    .line {
        display: flex;

        justify-content: center;
        align-items: center;
    }

    .cell {
        width: 70px;
        height: 70px;

        margin: 0 -1px -1px 0;

        display: flex;
        justify-content: center;
        align-items: center;

        background-color: #0c6490;
        
        border: 1px solid black;
    }
    .cell:hover {
        border-color: white;
        z-index: 10;
    }

    .revealed {
        background-color: #b8d9ea;
    }

    .flagged {
        background-image: url("./src/assets/flag.svg");
        background-size: 40px;
        background-repeat: no-repeat;
        background-position: center;
    }

    .doubt {
        background-color: #f0f0f0;
    }
</style>