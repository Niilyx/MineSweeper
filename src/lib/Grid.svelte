<script>
    const Game = class {
        constructor(w, h, minesTotal) {
            this.grid = [];
            this.gridSize = [w === undefined ? 10 : w, h === undefined ? 5 : h]
            this.minesTotal = minesTotal === undefined ? 10 : minesTotal;
        }
    };
    const Cell = class {
        constructor() {
            this.state = CELL_STATES.COVERED;
            this.mine = false;
        }
        nextState() {
            switch (this.state) {
                case CELL_STATES.UNCLICKED:
                    this.state = CELL_STATES.REVEALED;
                    break;
                case CELL_STATES.REVEALED:
                    this.state = CELL_STATES.FLAGGED;
                    break;
                case CELL_STATES.FLAGGED:
                    this.state = CELL_STATES.DOUBT;
                    break;
                case CELL_STATES.DOUBT:
                    this.state = CELL_STATES.UNCLICKED;
                    break;
            }
        }
    };
    const CELL_STATES = {
        UNCLICKED: 0,
        REVEALED: 1,
        FLAGGED: 2,
        DOUBT: 3,
    };


    let gameInstance;
    let mainElement;
    
    export const newGame = () => {
        gameInstance = new Game();
        initGrid();
    };
    const initGrid = () => {
        for (let i = 0; i < 9; i++) {
            const line = [];
            for (let j = 0; j < 9; j++) {
                line.push(new Cell());
            }
        }
    };
</script>

<main bind:this={mainElement}></main>

<style>
    main {
        margin: 0 auto;
        width: 80%;

        display: flex;
        justify-content: center;
        align-content: center;

        outline: black solid 1px;
    }
</style>