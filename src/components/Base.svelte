<script>
  import { fly } from 'svelte/transition';
  import Card from './Card.svelte';
  import Stats from './Stats.svelte';

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
  let showOverlay = false;
  let timeoutId;
  let ties = 0;
  let wins = 0;
  let losses = 0;
  let hasStarted = false;
  let playerMoney = 1000; 
  let betAmount = 100;
  let error = '';
  let betPlaced = false;

  // only display overlay after animations
  // equations gets amount of extra cards dealt
  $: if (gameOver) {
  timeoutId = setTimeout(() => {
      showOverlay = true;
  }, (Math.max(1, dealerHand.length -2)) * 100 + 400);
}

  function restart() {
    deck = [...newDeck]

    playerHand = [];
    dealerHand = [];
    gameOver = false;
    hasStood = false;
    showOverlay = false;
    timeoutId;
    ties = 0;
    wins = 0;
    losses = 0;
    hasStarted = false;
    playerMoney = 1000; 
    betAmount = 100;
    error = '';
    betPlaced = false;
  }

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
      deck = [...deck];
      return newHand;
  }

  function hit() {
    if (!gameOver && hasStarted) {
      playerHand = deal(playerHand);
      if (calculateHand(playerHand) > 21) {
          gameOver = true;
      }
    }
  }

  // for simulations
  function simulateHit() {
    while (calculateHand(playerHand) < 17) {
      playerHand = deal(playerHand);
    }
  }

  function placeBet(amount) {
    if (amount <= 0) {
      error = "Invalid Bet";
    } else if (playerMoney >= amount) {
      playerMoney -= amount;
      betAmount = amount;
      newGame();
      betPlaced = true;
      error = ""
    } else {
      error = "Invalid Bet";
    }
  }

  function newGame() {
    clearTimeout(timeoutId);
    hasStood = false
    gameOver = false;
    showOverlay = false;
    hasStarted = true
    playerHand = []
    dealerHand = []
    betPlaced = false;

    setTimeout(() => {
      playerHand = deal([]);
      dealerHand = deal([]);
      playerHand = deal(playerHand);
      dealerHand = deal(dealerHand);

      // playerHand = ['A♠', 'A♣'];
    }, 250);
  }


  function stand() {
    if (!gameOver && hasStarted) {
      while (calculateHand(dealerHand) < 17) {
        dealerHand = deal(dealerHand);
      }
      gameOver = true;
      hasStood = true;
    }
  }

  // doesn't trigger gameover so iterations can be at any interval
  function simulateStand() {
    while (calculateHand(dealerHand) < 17) {
      dealerHand = deal(dealerHand);
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

    let winner = 'Draw';
    if (playerScore > 21) {
      winner = 'Dealer';
    } else if (dealerScore > 21) {
      winner = 'Player';
    } else if (playerScore > dealerScore) {
      winner = 'Player';
    } else if (dealerScore > playerScore) {
      winner = 'Dealer';
    }

    if (winner === 'Player') {
      playerMoney += betAmount * 2; // player gets their bet back plus the same amount
      wins += 1
    } else if (winner === 'Draw') {
      playerMoney += betAmount; // player gets their bet back
      ties += 1
    } else {
      losses += 1
    }
    betPlaced = false;
    return winner;
  }

  async function simulateRounds(rounds) {
    for (let i = 0; i < rounds; i++) {

      placeBet(100);

      simulateHit();

      simulateStand();

      determineWinner();

      if (playerMoney == 0) {
        break
      }

      await new Promise(resolve => setTimeout(resolve, 50));
    }
  }


</script>

<main>
  <div class="blackjack-container">
    <div class="header">
      <h1 class="blackjack-header">BLACKJACK</h1>
    </div>

    <div class="mid">
      <div class="top-bar">
        <div class="betting">
          Bet:
          <input bind:value={betAmount} type="number" min="1" max={playerMoney} placeholder="100" disabled={betPlaced} />
        </div>
        <button on:click={() => placeBet(betAmount)} disabled={betPlaced}>Place</button>
        <button on:click={() => restart()}> Restart</button>
        <button on:click={() => simulateRounds(100)}>Simulate</button>
        {#if error}
          <p>{error}</p>
        {/if}
      </div>
      <div class="cards-container">
        <div id="dealer">
          <!-- {#if !gameOver && !hasStood}
            <h2>Dealer: ?</h2>
          {:else}
            <h2>Dealer: {calculateHand(dealerHand)}</h2>
          {/if} -->
          <div class="hand">
            {#each dealerHand as card, i (i)}          
              <div in:fly={{ y: -100, duration: 400, delay: i * 100}}>
              {#if i === 1 && !showOverlay && !hasStood}
                <Card isPlaceholder={true} />
              {:else}
                <Card {card} />
              {/if}
              </div>
            {/each}
          </div>
          <div class="deck"></div>
        </div>

        <div id="player">
          <div class="hand">
            {#each playerHand as card, i (i)}
              <div in:fly={{ y: 100, duration: 400, delay: i * 100}}>
                <Card {card} />
              </div>
            {/each}
          </div>
        </div>
      </div>
      <div class="player-info">
        {#if !gameOver && !hasStood}
          <h2>Dealer: ?</h2>
        {:else}
          <h2>Dealer: {calculateHand(dealerHand)}</h2>
        {/if}
        <div>Wins: {wins}</div>
        <div>Losses: {losses}</div>
        <div>Ties: {ties}</div>
        <div>Money: {playerMoney}</div>
        <h2>Player: {calculateHand(playerHand)}</h2>
      </div>
    </div>
    <div id="controls">
      <button on:click={hit}>Hit</button>
      <button on:click={stand}>Stand</button>
    </div>

    {#if showOverlay}
        <div class="overlay">
            <h2>Winner: {determineWinner()}</h2>
        </div>
    {/if}

    <Stats {deck} dealerSecondCard={dealerHand[1]} />

  </div>
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .blackjack-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%; /* Set this to match the container size you want */
    max-width: 733px; /* As per the image's apparent width */
    background: lightgray;
    border-radius: 20px;
    padding: 20px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Adjust shadow to your preference */
  }


  .top-bar {
    display: flex;
    flex-direction: column;
    width: 100%;
    justify-content: center;
  }

  .player-info {
    width: 100%;
    text-align: right;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  .header {
    display: flex;
    justify-content: space-between;
  }

  .mid {
    display: flex;
    width: 100%;
    justify-content: space-between;
  }

  .betting {
    padding: 5px;
  }

  .hand {
    display: flex;
    justify-content: center;
    flex-wrap: nowrap;
    flex-direction: columns;
    align-items: center;
    max-width: 100%;
    overflow: hidden;
  }

  #dealer, #player {
    margin-bottom: 1em;
    overflow: visible; /* Ensure the container allows for overflow */
    width: 100%; /* Ensure full utilization of the available width */
    min-height: 140px; /* Adjust based on card height to ensure container does not collapse */
  }

  .overlay {
    position: absolute;
    width: 50%;
    height: 10%;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgba(0, 0, 0, 0.5);
    color: white;
    font-size: 24px;
  }

  button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    background-color: #007BFF;
    color: white;
    font-size: 16px;
    cursor: pointer;
    width: 100px;
    margin: 5px;
  }

  input {
    width: 50px;
  }

  button:hover {
    background-color: #0056b3;
  }
</style>