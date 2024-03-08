<script>
  import Card from './Card.svelte';

  export let deck;
  export let dealerSecondCard = '';

  let vals = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
  let suits = ['♠', '♣', '♥', '♦'];
  let allCards = [];

  for (let suit of suits) {
    for (let val of vals) {
      allCards.push(val + suit);
    }
  }
</script>

<style>
  .card-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    width: 624px;
  }
  .card {
    margin: 4px;
    transition: opacity 0.3s ease;
  }
  .drawn {
    opacity: 0.4;
  }
</style>

<div class="card-container">
  {#each allCards as card}
    <div class="card {deck.includes(card) || card === dealerSecondCard ? '' : 'drawn'}">
      <Card card={card} width="40px" height="40px" normal=false/>
    </div>
  {/each}
</div>
