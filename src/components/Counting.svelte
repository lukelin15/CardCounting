<script>
  import { fade } from 'svelte/transition';
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();
  let mode = 'none'; // Default mode
  export let counts;

  const descriptions = {
    'none': 'No counting mode is selected. This is the default mode where cards are not tracked or counted.',
    'hi-lo': 'Hi-Lo counting mode. Each card is assigned a value of either -1, 0, or +1. It is a balanced system used to get an estimate of the high and low cards remaining in the deck.',
    'halves': 'Halves counting mode. A more complex system where cards are given values of -1, -0.5, 0, +0.5, or +1. It offers a more precise measure of the remaining deck composition than Hi-Lo.'
  };

  function changeMode(newMode) {
    mode = newMode;
    dispatch('modeChange', { mode: newMode }); // Dispatching an event with the new mode
  }

</script>

<div class="container">
  <div class="buttons">
    <button on:click={() => changeMode('none')}>None</button>
    <button on:click={() => changeMode('hi-lo')}>Hi-Lo</button>
    <button on:click={() => changeMode('halves')}>Halves</button>
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
