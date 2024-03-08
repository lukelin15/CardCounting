<script>
  import Card from './Card.svelte';

  export let deck;
  export let dealerSecondCard = '';

  let vals = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
  let suits = ['♠', '♣', '♥', '♦'];
  let allCards = [];
  let probabilities = [];

  for (let suit of suits) {
    for (let val of vals) {
      allCards.push(val + suit);
    }
  }

  function calculateProbabilities() {
    let totalCards = deck.length;
    probabilities = [];
    for (let val of vals) {
      let cnt = 0;
      for (let suit of suits) {
        if (deck.includes(val + suit) || val+suit === dealerSecondCard){
          cnt += 1
        }
      }
      probabilities.push(Math.round(cnt * 1000.0 / totalCards) / 10)
    }
  }

  $: deck && calculateProbabilities()
</script>

<div class="card-container">
  {#each allCards as card}
    <div class="card {deck.includes(card) || card === dealerSecondCard ? '' : 'drawn'}">
      <Card card={card} width="35px" height="40px" normal=false/>
    </div>
  {/each}
</div>
<div class="probabilities">
  {#each probabilities as prob}
    <div class="probability">{prob}%</div>
  {/each}
</div>



<style>
  .card-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    width: 600px;
    margin-top: 10px;
    padding-top: 10px;
    border-top: 2px solid black;
  }
  .card {
    margin: 4px;
    transition: opacity 0.3s ease;
  }
  .drawn {
    opacity: 0.4;
  }
  .probabilities {
    display: flex;
    justify-content: center;
    margin-top: 4px;
    box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);

  }
  .probability {
    width: 35px;
    text-align: center;
    padding: 3px;
    border: 1px solid #ccc;
  }

  .probability:first-child {
    border-top-left-radius: 10px;
    border-bottom-left-radius: 10px;
  }

  .probability:last-child {
    border-top-right-radius: 10px;
    border-bottom-right-radius: 10px;
  }
</style>