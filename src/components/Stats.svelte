<script>
  import Card from './Card.svelte';

  export let deck;
  export let dealerSecondCard = '';
  export let playerHand;

  let vals = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
  let suits = ['♠', '♣', '♥', '♦'];
  let allCards = [];
  let probabilities = [];

  let over21Odds;
  let to21Odds;

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
      let output = Math.round(cnt * 1000.0 / totalCards) / 10
      if (cnt == 0) {
        output = 0.0
      } else if (cnt == totalCards) {
        output = 100.0
      }
      probabilities.push(output)
    }
  }

  $: deck && calculateProbabilities()

  function calculateHandTotal(playerHand) {
    let total = 0;
    let aces = 0;

    playerHand.forEach(card => {
      let val = card.slice(0, -1); // Remove the suit
      if (val === 'A') {
        aces += 1;
        total += 11;
      } else if (['J', 'Q', 'K', '10'].includes(val)) {
        total += 10;
      } else {
        total += parseInt(val);
      }
    });

    // Adjust for aces if total is over 21
    while (total > 21 && aces > 0) {
      total -= 10;
      aces -= 1;
    }

    return total;
  }

  function calculateOdds() {
    const handTotal = calculateHandTotal(playerHand);
    let totalCards = deck.length;
    let to21Count = 0;
    let over21Count = 0;

    vals.forEach(val => {
      let cardValue = (['J', 'Q', 'K', '10'].includes(val)) ? 10 : (val === 'A' ? 1 : parseInt(val));
      suits.forEach(suit => {
        if (deck.includes(val + suit)) {
          if (handTotal + cardValue > 21) {
            over21Count += (val === 'A' && handTotal + 11 <= 21) ? 0 : 1;
          } else if (handTotal + cardValue === 21) {
            to21Count += 1;
          }
        }
      });
    });

    to21Odds = Math.round(to21Count * 10000 / totalCards) / 100;
    over21Odds = Math.round(over21Count * 10000 / totalCards) / 100;
  }

  // Reactive statement to recalculate odds when deck or playerHand changes
  $: if (deck && playerHand) {
    calculateOdds();
  }

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
<div class="odds">
  <div class="odd">Odds of getting 21: <b>{to21Odds}%</b></div>
  <div class="odd">Odds of busting: <b>{over21Odds}%</b></div>
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
  .odds {
    display: flex;
    justify-content: center;
    margin-top: 5px;
    box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
  }
  .odd {
    text-align: center;
    padding: 3px;
    border: 1px solid #ccc;
  }
  .odd:first-child {
    border-top-left-radius: 10px;
    border-bottom-left-radius: 10px;
  }
  .odd:last-child {
    border-top-right-radius: 10px;
    border-bottom-right-radius: 10px;
  }
</style>