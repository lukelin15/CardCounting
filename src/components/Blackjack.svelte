<script>
    import { fly } from 'svelte/transition';
    import Card from './Card.svelte'

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
    let hasStood = false;
  
    function shuffle() {
      for (let i = deck.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [deck[i], deck[j]] = [deck[j], deck[i]];
      }
    }

    shuffle()
  
    function deal(hand) {
      if (deck.length == 0) {
        deck = [...newDeck]
        shuffle()
      }
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
        hasStood = true;
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
      {#if !gameOver && !hasStood}
        <h2>Dealer: ?</h2>
      {:else}
        <h2>Dealer: {calculateHand(dealerHand)}</h2>
      {/if}
      <div class="hand">
        {#each dealerHand as card, i (i)}          
          <div in:fly={{ y: -100, duration: 400, delay: (i+1) * 100 }}>
          {#if i === 1 && !gameOver && !hasStood}
            <Card isPlaceholder={true} />
          {:else}
            <Card {card} />
          {/if}
          </div>
        {/each}
      </div>
    </div>
  
    <div id="player">
      <h2>Player: {calculateHand(playerHand)}</h2>
      <div class="hand">
        {#each playerHand as card, i (i)}
          <div in:fly={{ y: 100, duration: 400, delay: (i+1) * 100 }}>
            <Card {card} />
          </div>
        {/each}
      </div>
    </div>
  
    <div id="controls">
      <button on:click={newGame}>New Game</button>
      <button on:click={hit}>Hit</button>
      <button on:click={stand}>Stand</button>
    </div>
  
    {#if gameOver}
        <div class="overlay">
            <h2>Winner: {determineWinner()}</h2>
        </div>
    {/if}
</main>
  
<style>
    main {
      position: relative;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: space-between;
      width: 400px; /* Adjust the size to your preference */
      height: 400px; /* Adjust the size to your preference */
      margin: auto;
      padding: 20px;
      border: 1px solid #ccc;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
      background-color: #f9f9f9;
    }
  
    .hand {
      display: flex;
      justify-content: center;
      flex-wrap: nowrap;
      max-width: 100%;
      overflow: hidden;
    }
  
    #dealer, #player {
      margin-bottom: 1em;
      overflow: visible; /* Ensure the container allows for overflow */
      width: 100%; /* Ensure full utilization of the available width */
      min-height: 140px; /* Adjust based on card height to ensure container does not collapse */
    }
  
    #controls {
      position: relative;
      display: flex;
      justify-content: center;
      gap: 1em;
      bottom: 0;
    }

    .overlay {
      position: absolute;
      width: 70%;
      height: 70%;
      display: flex;
      justify-content: center;
      align-items: center;
      background-color: rgba(0, 0, 0, 0.5);
      color: white;
      font-size: 24px;
    }
</style>