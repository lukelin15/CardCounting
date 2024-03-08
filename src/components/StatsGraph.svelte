<script>
  import { scaleLinear } from 'd3-scale';
  import { max } from 'd3-array';

  export let winningCards = [];

  // Extract card values from winningCards
  $: cardValues = winningCards.map(card => card.slice(0, -1));

  // Count occurrences of each card value
  $: counts = cardValues.reduce((acc, value) => {
    acc[value] = (acc[value] || 0) + 1;
    return acc;
  }, {});

  // Define all possible card values for histogram
  let vals = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];

  // Calculate maximum count for scaling
  $: maxCount = max(Object.values(counts));

  // Create a linear scale for the bar heights
  $: heightScale = scaleLinear()
    .domain([0, maxCount || 1])
    .range([0, 100]); // Maximum bar height in pixels
</script>


<div class="histogram">
  {#each vals as val}
    <div>
      <div class="bar" style="height: {heightScale(counts[val] || 0)}px"></div>
      <div class="bar-label">{val}</div>
    </div>
  {/each}
</div>

<style>
  .histogram {
    display: flex;
    align-items: flex-end;
    height: 120px; /* Total height of the histogram */
    width: 0px;
    gap: 4px;
  }

  .bar {
    width: 20px; /* Width of each bar */
    background-color: steelblue;
    transition: height 0.5s ease; /* Animate height changes */
  }

  .bar-label {
    text-align: center;
    font-size: 10px;
    margin-top: 5px;
  }
</style>
