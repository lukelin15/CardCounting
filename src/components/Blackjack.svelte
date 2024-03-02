<script>
    import { fly } from 'svelte/transition';

    // deck initializing stuff
    let vals = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
    let suits = ['♠', '♣', '♥', '♦'];
    let newDeck = [];
    
    for (let suit of suits) {
      for (let val of vals) {
        newDeck.push(val + suit);
      }
    }

    // newDeck is a template to copy decks from
    let deck = [...newDeck]
  
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
        if (!gameOver) {
          playerHand = deal(playerHand);
          if (calculateHand(playerHand) > 21) {
              gameOver = true;
          }
        }
    }

    function newGame() {
        shuffle();
        playerHand = deal([]);
        dealerHand = deal([]);
        playerHand = deal(playerHand);
        dealerHand = deal(dealerHand);
        gameOver = false;
    }
  
    function stand() {
      if (!gameOver) {
        while (calculateHand(dealerHand) < 17) {
          dealerHand = deal(dealerHand);
        }
        gameOver = true;
      }
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
    <div id="dealer">
      <h2>Dealer</h2>
      <div class="hand">
        {#each dealerHand as card, i (i)}
        <div class="card" in:fly="{{ y: -200, duration: 500 }}">{card}</div>
        {/each}
      </div>
      <!-- <div>Score: {calculateHand(dealerHand)}</div> -->
    </div>
  
    <div id="player">
      <h2>Player</h2>
      <div class="hand">
        {#each playerHand as card, i (i)}
        <div class="card" in:fly="{{ y: 200, duration: 500 }}">{card}</div>
        {/each}
      </div>
      <!-- <div>Score: {calculateHand(playerHand)}</div> -->
    </div>
  
    <div id="controls">
      <button on:click={newGame}>New Game</button>
      <button on:click={hit}>Hit</button>
      <button on:click={stand}>Stand</button>
    </div>
  
    {#if gameOver}
        <div>
            <h2>Winner</h2>
            <div>{determineWinner()}</div>
        </div>
    {/if}
</main>
  
<style>
    main {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      width: 300px; /* Adjust the size to your preference */
      height: 300px; /* Adjust the size to your preference */
      margin: auto;
      padding: 20px;
      border: 1px solid #ccc;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
      background-color: #f9f9f9;
    }

    .card {
      font-size: 1em;
      text-align: center;
      padding: 1em;
      border: 1px solid #ccc;
      border-radius: 5px;
      margin: 1em;
      background-color: white;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.15);
    }
  
    .hand {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
    }
  
    #dealer, #player {
      margin-bottom: 1em;
    }
  
    #controls {
      display: flex;
      justify-content: center;
      gap: 1em;
    }
</style>