<script>
  import { scaleLinear } from 'd3-scale';
  import { max } from 'd3-array';
  import { onMount } from 'svelte';

  export let winningCards = [];

  $: cardValues = winningCards.map(card => card.slice(0, -1));
  $: totalCards = winningCards.length;

  $: counts = cardValues.reduce((acc, value) => {
    acc[value] = (acc[value] || 0) + 1;
    return acc;
  }, {});

  let vals = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
  $: maxCount = max(Object.values(counts));
  $: heightScale = scaleLinear().domain([0, maxCount || 1]).range([0, 100]);

  let tooltipContent = '';
  let tooltipVisible = false;
  let tooltipX = 0;
  let tooltipY = 0;

  function showTooltip(event, val) {
    const cardCount = counts[val] || 0;
    const proportion = ((cardCount / totalCards) * 100).toFixed(2);
    tooltipContent = `${val}: ${cardCount} cards (${proportion}%)`;
    tooltipX = event.clientX;
    tooltipY = event.clientY - 20; // Position above the cursor
    tooltipVisible = true;
  }

  function hideTooltip() {
    tooltipVisible = false;
  }
</script>

<h2 class="title">Winning Hands</h2>
<div class="histogram">
  {#each vals as val}
    <div
      role="presentation" 
      on:mouseenter={(event) => showTooltip(event, val)} 
      on:mouseleave={hideTooltip}
    >
      <div class="bar" style="height: {heightScale(counts[val] || 0)}px"></div>
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
