<script>
    let deck = [
      'A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K',
      'A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K',
      'A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K',
      'A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'
    ];
    let suits = ['♠', '♣', '♥', '♦'];
    deck = deck.map(card => {
      return card + suits[Math.floor(Math.random() * suits.length)];
    });
  
    let playerHand = [];
    let dealerHand = [];
    let gameOver = false;
  
    function shuffle() {
      for (let i = deck.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [deck[i], deck[j]] = [deck[j], deck[i]];
      }
    }
  
    function deal(hand) {
        let newHand = [...hand, deck.pop()];
        return newHand;
    }

    function hit() {
        playerHand = deal(playerHand);
    }

    function newGame() {
        shuffle();
        playerHand = deal([]);
        dealerHand = deal([]);
        playerHand = deal(playerHand);
        dealerHand = deal(dealerHand);
    }
  
    function stand() {
      while (calculateHand(dealerHand) < 17) {
        deal(dealerHand);
      }
      gameOver = true;
    }
  
    function calculateHand(hand) {
      let sum = 0;
      let aces = 0;
  
      for (let card of hand) {
        let value = card.slice(0, -1);
        if (value === 'A') {
          aces += 1;
          sum += 11;
        } else if (['K', 'Q', 'J'].includes(value)) {
          sum += 10;
        } else {
          sum += parseInt(value);
        }
      }
  
      while (sum > 21 && aces > 0) {
        sum -= 10;
        aces -= 1;
      }
  
      return sum;
    }

    function determineWinner() {
        let playerScore = calculateHand(playerHand);
        let dealerScore = calculateHand(dealerHand);

        if (playerScore > 21) {
            return 'Dealer';
        } else if (dealerScore > 21) {
            return 'Player';
        } else if (playerScore > dealerScore) {
            return 'Player';
        } else if (dealerScore > playerScore) {
            return 'Dealer';
        } else {
            return 'Draw';
        }
    }

</script>
  
<main>
    <button on:click={newGame}>New Game</button>
    <button on:click={hit}>Hit</button>
    <button on:click={stand}>Stand</button>

    <div>
        <h2>Player</h2>
        {#each playerHand as card (card)}
        <div class="card">{card}</div>
        {/each}
        <div>Score: {calculateHand(playerHand)}</div>
    </div>

    <div>
        <h2>Dealer</h2>
        {#each dealerHand as card (card)}
        <div class="card">{card}</div>
        {/each}
        <div>Score: {calculateHand(dealerHand)}</div>
    </div>

    {#if gameOver}
    <div>
      <h2>Winner</h2>
      <div>{determineWinner()}</div>
    </div>
  {/if}
</main>
  
  <style>
    .card {
      font-size: 3em;
      text-align: center;
      padding: 1em;
      border: 1px solid #ccc;
      border-radius: 5px;
      margin: 1em;
    }
  </style>