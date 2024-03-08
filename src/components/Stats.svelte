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

<div class="container">
  <h2>Deck</h2>
  {#each vals as val, valIndex}
    <div class="row">
      {#each suits as suit}
        <div class="card {deck.includes(val + suit) || val + suit === dealerSecondCard ? '' : 'drawn'}">
          <Card card={val + suit} width="35px" height="40px" normal=false/>
        </div>
      {/each}
      <div class="probability">{probabilities[valIndex]}%</div>
    </div>
  {/each}
  <div class="odds">
    <div class="odd">
      <div>Odds of getting 21:</div>
      <div><b>{to21Odds}%</b></div>
    </div>
    <div class="odd">
      <div>Odds of busting:</div>
      <div><b>{over21Odds}%</b></div>
    </div>
  </div>
</div>


<style>
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 300px;
  }

  .row {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 1px; /* Space between rows */
  }

  .card {
    margin: 1px;
    transition: opacity 0.3s ease;
  }

  .drawn {
    opacity: 0.4;
  }

  .probability {
    margin-left: 10px; /* Space between cards and probability */
    padding: 3px;
    border: 1px solid #ccc;
    box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
    width: 50px;
  }

  .odds {
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
    margin-top: 5px;
    width: 200px;
    box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
  }

  .odd {
    display: flex;
    justify-content: space-between;
    width: 200px;
  }

</style>