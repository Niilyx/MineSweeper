<script>
  import Grid from "./lib/Grid.svelte";
  
  const DIFFICULTIES = {
    "FACILE": {
      minesTotal: 10,
      width: 9,
      height: 9,
    },
    "NORMAL": {
      minesTotal: 40,
      width: 16,
      height: 16,
    },
    "DIFFICILE": {
      minesTotal: 99,
      width: 30,
      height: 16,
    },
    "CUSTOM": {
      minesTotal: 40,
      width: 16,
      height: 16,
    }
  }
  let difficulty = DIFFICULTIES.NORMAL;

  let grid;
  const newGame = () => {
    grid.newGame(difficulty.minesTotal, difficulty.width, difficulty.height);
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
        Difficile
      </label>

      <label>
        <input type=radio bind:group={difficulty} name="difficulty" value={DIFFICULTIES.CUSTOM}/>
        Personnalisé : 
        <input disabled={difficulty !== DIFFICULTIES.CUSTOM} bind:value={DIFFICULTIES.CUSTOM.minesTotal} type=number min=1 max=100/>
        <input disabled={difficulty !== DIFFICULTIES.CUSTOM} bind:value={DIFFICULTIES.CUSTOM.width} type=number min=1 max=50/>
        <input disabled={difficulty !== DIFFICULTIES.CUSTOM} bind:value={DIFFICULTIES.CUSTOM.height} type=number min=1 max=50/>
      </label>
      
      <button class="newGameBtn" on:click={newGame}>
        Nouvelle Partie
      </button>

    </div>
    <div id="grid">
      <Grid bind:this={grid}/>
    </div>
  </div>
</main>

<style>
  main {
    margin: 0 auto;
    color: white;
  }
  label {
    display: inline;
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

    display: flex;
    justify-content: center;
    align-content: center;
  }
  .HUD {
    width: clamp(300px, 80%, 600px);
    display: inline-flex;
    justify-content: center;
    align-items: center;
  }
  .newGameBtn {
    margin-left: 10%;
    margin-right: -12%;
  }
</style>
