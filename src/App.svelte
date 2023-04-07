<script>
  import NewGameButton from './lib/NewGameButton.svelte';
</script>

<main>
  <div id="wrapper">
    <div class="HUD">
        <NewGameButton />
    </div>
    <div id="grid">

    </div>
  </div>
</main>

<style>
  main {
    width: 100%;
    height: 100%;
    margin: 0 auto;
    background-color: #131516;
    color: white;
  }
  #wrapper {
    margin: 0 auto;
    width: 100%;
    display: flex;

    justify-content: center;
    align-items: center;

    flex-direction: column;
  }
  #grid {
    margin: 0 auto;
    width: 80%;

    display: flex;
    justify-content: center;
    align-content: center;

    outline: black solid 1px;
  }
</style>
