<script>
  import { fade } from 'svelte/transition';
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();
  let mode = 'none'; // Default mode
  export let counts;

  const descriptions = {
    'none': 'Trust your Gut',
    'hi-lo': 'Use 1s and 0s',
    'halves': 'Halves counting mode'
  };

  const cardValues = {
    'hi-lo': { '2': 1, '3': 1, '4': 1, '5': 1, '6': 1, '7': 0, '8': 0, '9': 0, '10': -1, 'J': -1, 'Q': -1, 'K': -1, 'A': -1 },
    'halves': { '2': 0.5, '3': 1, '4': 1, '5': 1.5, '6': 1, '7': 0.5, '8': 0, '9': -0.5, '10': -1, 'J': -1, 'Q': -1, 'K': -1, 'A': -1 }
  };

  function changeMode(newMode) {
    mode = newMode;
    dispatch('modeChange', { mode: newMode }); // Dispatching an event with the new mode
  }

</script>

<div class="container">
  <div class="counting">
    <div class="buttons">
    <button on:click={() => changeMode('none')} disabled={mode === 'none'}>None</button>
    <button on:click={() => changeMode('hi-lo')} disabled={mode === 'hi-lo'}>Hi-Lo</button>
    <button on:click={() => changeMode('halves')} disabled={mode === 'halves'}>Halves</button>
  </div>
  <div class="description" transition:fade>
    {descriptions[mode]}
  </div>
  {#if mode !== 'none'}
    <table>
      <tr><th>Card</th><th>Value</th></tr>
      {#each Object.entries(cardValues[mode] || {}) as [card, value]}
        <tr>
          <td>{card}</td>
          <td>{value}</td>
        </tr>
      {/each}
    </table>
    <div class="count">Count: {counts}</div>
  {/if}
  
</div>
</div>

<style>
  .container {
    width: 300px;
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center; /* Add this line to center children horizontally */
  }
  .buttons button {
    border: none;
    width: 50px;
    height: 30px;
    border-radius: 5px;
    background-color: #007bff;
    color: white;
    cursor: pointer;
    transition: background-color 0.3s;
    margin-bottom: 5px;
  }
  .buttons button:disabled {
    background-color: #0056b3;
    cursor: default;
  }
  .description {
    transition: color 0.4s ease;
  }
  .counting {
    height: 400px;
  }
  table {
    width: 150px;
    border-collapse: collapse;
    margin-top: 10px;
  }
  th, td {
    border: 1px solid #ddd;
    padding: 2px;
  }
  th {
    background-color: #f2f2f2;
  }
</style>
