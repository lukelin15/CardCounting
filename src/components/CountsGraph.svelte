<script>
  import { scaleLinear } from 'd3-scale';
  import { max } from 'd3-array';
  import { onMount } from 'svelte';

  export let runningWins = [];
  export let runningLoss = [];

  // First, calculate counts of values in runningWins and runningLoss separately
  $: winsCounts = runningWins.reduce((acc, value) => {
    acc[value] = (acc[value] || 0) + 1;
    return acc;
  }, {});

  $: lossCounts = runningLoss.reduce((acc, value) => {
    acc[value] = (acc[value] || 0) + 1;
    return acc;
  }, {});

  // Then, calculate probabilities for each distinct value
  $: probabilities = Object.keys({...winsCounts, ...lossCounts}).reduce((acc, value) => {
    const wins = winsCounts[value] || 0;
    const losses = lossCounts[value] || 0;
    const total = wins + losses;
    acc[value] = total > 0 ? (wins / total) * 100 : 0;
    return acc;
  }, {});

  // Define all possible count values (assuming the range is -5 to 5 for simplification)
  let vals = Array.from({length: 9}, (_, i) => i - 4);

  $: maxProbability = max(Object.values(probabilities));
  $: heightScale = scaleLinear().domain([0, maxProbability || 1]).range([0, 100]);

  let tooltipContent = '';
  let tooltipVisible = false;
  let tooltipX = 0;
  let tooltipY = 0;

  function showTooltip(event, val) {
    const probability = probabilities[val] || 0;
    const totalOccurrences = (winsCounts[val] || 0) + (lossCounts[val] || 0);
    tooltipContent = `Count ${val}: ${probability.toFixed(2)}% chance of winning (Total: ${totalOccurrences})`;
    tooltipX = event.clientX;
    tooltipY = event.clientY - 20; // Position above the cursor
    tooltipVisible = true;
  }


  function hideTooltip() {
    tooltipVisible = false;
  }
</script>

<h2 class="title">Running Wins Distribution</h2>
<div class="histogram">
  {#each vals as val}
    <div
      role="presentation" 
      on:mouseenter={(event) => showTooltip(event, val)} 
      on:mouseleave={hideTooltip}
      aria-label={`Wins at ${val}`}
    >
      <div class="bar" style="height: {heightScale(probabilities[val] || 0)}px"></div>
      <div class="bar-label">{val}</div>
    </div>
  {/each}
</div>

{#if tooltipVisible}
  <div class="tooltip" style="left: {tooltipX}px; top: {tooltipY}px;">{tooltipContent}</div>
{/if}

<style>
  .title {
    text-align: center;
    font-size: 16px;
    margin-bottom: 10px;
  }

  .histogram {
    display: flex;
    align-items: flex-end;
    height: 120px;
    gap: 4px;
  }

  .bar {
    width: 20px;
    background-color: steelblue;
    transition: height 0.5s ease;
  }

  .bar-label {
    text-align: center;
    font-size: 10px;
    margin-top: 5px;
  }

  .tooltip {
    position: fixed;
    padding: 4px 8px;
    background-color: black;
    color: white;
    border-radius: 4px;
    font-size: 12px;
    pointer-events: none; /* Allows mouse events to pass through */
    z-index: 10; /* Ensure tooltip is above other elements */
  }
</style>
