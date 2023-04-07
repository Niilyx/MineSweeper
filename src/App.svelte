<script>
  import Grid from "./lib/Grid.svelte";

  let grid;
  const newGame = () => {
    grid.newGame();
  }
</script>

<h1>Démineur</h1>
<main>
  <div id="wrapper">
    <div class="HUD">
      <button on:click={newGame}>
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
    width: 100%;
    height: 100%;
    margin: 0 auto;
    color: white;
  }
  #wrapper {
    margin: 0 auto;
    width: 100%;
    height: 90%;
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
</style>
