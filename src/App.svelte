<script>
// @ts-nocheck

    import Grid from "./lib/Grid.svelte";

    const MAX_MINES = 499;
    const MAX_WIDTH = 50;
    const MAX_HEIGHT = 50;

    let badMineInput = false;
    let badWidthInput = false;
    let badHeightInput = false;

    let failToStart = false;

    let gameInstance;

    const onCustomInputChange = (event) => {
        event.target.classList.remove("badInput");
        if (event.target.value === "") return;

        const value = parseInt(event.target.value);
        if (isNaN(value) || value < event.target.min) {
            event.target.value = event.target.min;
        }
        if (value > event.target.max) {
            event.target.value = event.target.max;
        }
    }
    
    const DIFFICULTIES = {
        "FACILE": {
            minesTotal: 10,
            width: 12,
            height: 12,
        },
        "NORMAL": {
            minesTotal: 46,
            width: 20,
            height: 20,
        },
        "DIFFICILE": {
            minesTotal: 400,
            width: 60,
            height: 45,
        },
        "CUSTOM": {
            minesTotal: null,
            width: null,
            height: null,
        }
    }
    let difficulty = DIFFICULTIES.NORMAL;

    let grid;
    const validateInput = () => {
        let atLeastOneBadInput = false;
        if (!difficulty.minesTotal) {
            badMineInput = true;
            atLeastOneBadInput = true;
        }
        if (!difficulty.width) {
            badWidthInput = true;
            atLeastOneBadInput
        }
        if (!difficulty.height) {
            badHeightInput = true;
            atLeastOneBadInput = true;
        }
        if (atLeastOneBadInput) return false;

        if (difficulty === DIFFICULTIES.CUSTOM) {
            if (difficulty.minesTotal > MAX_MINES) {
                difficulty.minesTotal = MAX_MINES;
            }
            if (difficulty.width > MAX_WIDTH) {
                difficulty.width = MAX_WIDTH;
            }
            if (difficulty.height > MAX_HEIGHT) {
                difficulty.height = MAX_HEIGHT;
            }
        }
        return true;
    }
    const newGame = () => {
        if (!validateInput()) return;
        const game = grid.newGame(difficulty.minesTotal, difficulty.width, difficulty.height);
        failToStart = !game;
        badMineInput = failToStart;

        gameInstance = game;
    }
</script>






<h1>Démineur</h1>
<main>
    <div id="wrapper">
        <div class="HUD">
            <label>
                <input type=radio bind:group={difficulty} name="difficulty" value={DIFFICULTIES.FACILE}/>
                Facile
            </label>

            <label>
                <input type=radio bind:group={difficulty} name="difficulty" value={DIFFICULTIES.NORMAL}/>
                Normal
            </label>

            <label>
                <input type=radio bind:group={difficulty} name="difficulty" value={DIFFICULTIES.DIFFICILE}/>
                💀💀💀
            </label>

            <label>
                <input type=radio bind:group={difficulty} name="difficulty" value={DIFFICULTIES.CUSTOM}/>
                Personnalisé :<br>
                {#if difficulty === DIFFICULTIES.CUSTOM}
                    <input style="width: 34px;" class:badInput={badMineInput} on:input={onCustomInputChange} placeholder="1-{MAX_MINES}" disabled={difficulty !== DIFFICULTIES.CUSTOM} bind:value={DIFFICULTIES.CUSTOM.minesTotal} type=number min=1 max={MAX_MINES}/> mines <br>
                    <input class:badInput={badWidthInput} on:input={onCustomInputChange} placeholder="1-{MAX_WIDTH}" disabled={difficulty !== DIFFICULTIES.CUSTOM} bind:value={DIFFICULTIES.CUSTOM.width} type=number min=1 max={MAX_WIDTH}/>
                    x
                    <input class:badInput={badHeightInput} on:input={onCustomInputChange} placeholder="1-{MAX_HEIGHT}" disabled={difficulty !== DIFFICULTIES.CUSTOM} bind:value={DIFFICULTIES.CUSTOM.height} type=number min=1 max={MAX_HEIGHT}/>
                {/if}
            </label>
            
            <button class="newGameBtn" on:click={newGame}>
                Nouvelle Partie
            </button>

        </div>
        {#if failToStart}<p class="tooManyMines">Trop de mines par rapport à la taille de la grille</p>{/if}
        <div id="grid">
            <Grid bind:this={grid} on:cellClick={() => gameInstance = gameInstance} />
        </div>
        {#if grid && gameInstance && !failToStart}
        <div class="underGrid">
            <!-- svelte-ignore a11y-missing-attribute -->
            <img style="margin: 5px;" src="/src/assets/icons/mine.png" />
            {#each gameInstance.minesTotal - gameInstance.minesFlagged < 0 ? "0" : (gameInstance.minesTotal - gameInstance.minesFlagged).toString() as digit}
                <img src="/src/assets/numbers/{digit}.png" />
            {/each}
        </div>
        {/if}
    </div>
</main>






<style>
    main {
        margin: 0 auto;
        color: white;
    }
    label {
        display: inline;
        margin: 0 8px;
        user-select: none;
    }

    img {
        pointer-events: none;
        user-select: none;
    }

    /* Chrome, Safari, Edge, Opera */
    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
        -webkit-appearance: none;
        margin: 0;
    }
    input[type=number] {
        /* Firefox */
        appearance: textfield;
        -moz-appearance: textfield;

        width: 28px;
    }

    .badInput {
        border: 2px solid red;
        border-radius: 3px;
    }

    .tooManyMines {
        color: red;
        font-family: Consolas, monospace;
    }

    #wrapper {
        margin: 0 auto;
        display: flex;

        justify-content: center;
        align-items: center;

        flex-direction: column;
    }
    #grid {
        margin: 0 auto;
        max-width: 10%;
        max-height: 10%;

        display: flex;
        justify-content: center;
        align-content: center;
    }
    .HUD {
        margin: 10px 0;
        display: inline-flex;
        justify-content: center;
        align-items: center;
    }
    .newGameBtn {
        margin-left: 10%;
        margin-right: -12%; /* Merci CSS */

        user-select: none;
    }

    .underGrid {
        margin: 0;
        display: flex;
        justify-content: center;
        align-items: center;
    }
</style>
