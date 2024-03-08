<script>
  export let card;
  export let isPlaceholder = false;
  export let width = '80px';
  export let height = '120px';
  export let normal = true;

  // $ for reactivity
  $: value = isPlaceholder ? '' : card.slice(0, -1);
  $: suit = isPlaceholder ? '' : card.slice(-1);
  $: isRed = suit === '♥' || suit === '♦';

</script>

<div class="card" class:placeholder={isPlaceholder} style="width: {width}; height: {height};">
  <div class="card-top" class:icon={normal=="false"}>
    <span class="card-value" class:red={isRed}>{value}</span>
    <span class="card-suit" class:red={isRed}>{suit}</span>
  </div>
  {#if normal == true}
    <div class="card-middle" class:red={isRed}>
        <span class="card-suit">{suit}</span>
    </div>
    <div class="card-bottom">
      <span class="card-value" class:red={isRed}>{value}</span>
      <span class="card-suit" class:red={isRed}>{suit}</span>
    </div>
  {/if}
</div>

<style>
  .card {
    display: flex;
    flex-direction: column;
    justify-content: center;
    position: relative;
    border: 1px solid #ccc;
    border-radius: 10px;
    background-color: white;
    box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
    padding: 10px;
    box-sizing: border-box;
    font-family: 'Arial', sans-serif;
  }

  .card-top, .card-bottom {
    display: flex;
    flex-direction: row;
  }

  .card-top .card-value, .card-top .card-suit, .card-bottom .card-value, .card-bottom .card-suit {
    font-size: 14px;
  }

  .card-middle .card-suit {
    font-size: 32px;
  }

  .card-bottom {
    transform: rotate(180deg);
    position: absolute;
    bottom: 10px;
    right: 10px;
  }

  .card-top {
    position: absolute;
    top: 10px; 
    left: 10px;
  }

  .card-top.icon {
    position: absolute;
    top: auto; 
    left: auto;
  }

  .card-middle {
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .red {
    color: red;
  }

  .placeholder {
    background-color: grey;
    color: transparent;
    border: none;
  }
</style>
