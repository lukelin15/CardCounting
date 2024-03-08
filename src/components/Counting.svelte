<script>
  import { fade } from 'svelte/transition';

  let mode = 'none'; // Default mode

  export let counts;

  const descriptions = {
    'none': 'No counting mode is selected. This is the default mode where cards are not tracked or counted.',
    'hi-lo': 'Hi-Lo counting mode. Each card is assigned a value of either -1, 0, or +1. It is a balanced system used to get an estimate of the high and low cards remaining in the deck.',
    'halves': 'Halves counting mode. A more complex system where cards are given values of -1, -0.5, 0, +0.5, or +1. It offers a more precise measure of the remaining deck composition than Hi-Lo.'
  };
</script>

<div class="container">
  <div class="buttons">
    <button on:click={() => mode = 'none'}>None</button>
    <button on:click={() => mode = 'hi-lo'}>Hi-Lo</button>
    <button on:click={() => mode = 'halves'}>Halves</button>
  </div>
  <div class="description" transition:fade>
    {descriptions[mode]}
  </div>
  <div class="count">Count: {counts}</div>
</div>

<style>
  .container {
    width: 300px;
    text-align: center;
  }
  .buttons {
    margin-bottom: 20px;
  }
  .description {
    transition: color 0.4s ease;
  }
</style>
