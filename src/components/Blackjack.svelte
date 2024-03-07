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
  let playerSplitHand = [];
  let dealerHand = [];
  let gameOver = false;
  let gameOverSplit = false;
  let hasStood = false;
  let showOverlay = false;
  let timeoutId;
  let hasStarted = false;
  let playerMoney = 1000; 
  let betAmount = 0;
  let error = '';
  let betPlaced = false;
  let haveSplit = false; 
  let cansplit = false;

  // only display overlay after animations
  // equations gets amount of extra cards dealt
  $: if (gameOver) {
  timeoutId = setTimeout(() => {
      showOverlay = true;
  }, (Math.max(1, dealerHand.length -2)) * 100 + 400);
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
    cansplit = false;
    if (!gameOver && hasStarted) {
      playerHand = deal(playerHand);
      if (calculateHand(playerHand) > 21) {
          gameOver = true;
      }
    }
  }

  function doubleDown() {
    if (playerHand.length === 2 && playerMoney >= betAmount * 2) {
      betAmount *= 2;
      hit();
      stand();
    }
  }
  
  function hitSplit() {
    if (!gameOverSplit && hasStarted) {
      playerSplitHand = deal(playerSplitHand);
      if (calculateHand(playerSplitHand) > 21) {
          gameOverSplit = true;
      }
    }
  }

  function canSplit() {
    return playerHand.length === 2 && playerHand[0][0] === playerHand[1][0] && playerMoney >= betAmount;
  }

  function split() {
    if (canSplit()) {
      playerMoney -= betAmount; 
      betAmount *= 2;
      playerSplitHand = [playerHand.pop()];
      playerHand = [playerHand[0]];
      haveSplit = true;
    }
  }

  function placeBet(amount) {
    if (playerMoney >= amount) {
      playerMoney -= amount;
      betAmount = amount;
      newGame();
      betPlaced = true;
    } else {
      error = "You don't have enough money to place this bet.";
    }
  }

  function newGame() {
    clearTimeout(timeoutId);
    hasStood = false
    gameOver = false;
    gameOverSplit = false;
    showOverlay = false;
    hasStarted = true
    playerHand = []
    playerSplitHand = []
    dealerHand = []
    betPlaced = false;
    haveSplit = false;
    cansplit = false;

    setTimeout(() => {
      playerHand = deal([]);
      dealerHand = deal([]);
      playerHand = deal(playerHand);
      dealerHand = deal(dealerHand);

      // playerHand = ['A♠', 'A♣'];

      if (canSplit()) {
        cansplit = true;
      }
    }, 250);
  }


  function stand() {
    if (!gameOver && hasStarted) {
      while (calculateHand(dealerHand) < 17) {
        dealerHand = deal(dealerHand);
      }
      gameOver = true;
      gameOverSplit = true;
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
    } else if (winner === 'Draw') {
      playerMoney += betAmount; // player gets their bet back
    }
    betPlaced = false;
    return winner;
  }

  function determineWinnerSplit() {
    let playerSplitScore = calculateHand(playerSplitHand);
    let dealerScore = calculateHand(dealerHand);

    let winner = 'Draw';
    if (playerSplitScore > 21) {
      winner = 'Dealer';
    } else if (dealerScore > 21) {
      winner = 'Player';
    } else if (playerSplitScore > dealerScore) {
      winner = 'Player';
    } else if (dealerScore > playerSplitScore) {
      winner = 'Dealer';
    }

    if (winner === 'Player') {
      playerMoney += betAmount * 2; // player gets their bet back plus the same amount
    } else if (winner === 'Draw') {
      playerMoney += betAmount; // player gets their bet back
    }
    betPlaced = false;
    return winner;
  }

  function basicStrategy() {
    let playerScore = calculateHand(playerHand);
    let dealerUpcard = dealerHand[0].slice(0, -1);
    let dealerUpcardValue = ['K', 'Q', 'J'].includes(dealerUpcard) ? 10 : parseInt(dealerUpcard);
    let isSoftHand = playerHand.some(card => card.slice(0, -1) === 'A');
    let isPair = playerHand.length === 2 && playerHand[0][0] === playerHand[1][0];

    if (isPair) {
      let pairValue = playerHand[0].slice(0, -1);
      if (['A', '8'].includes(pairValue) || 
          (['2', '3'].includes(pairValue) && dealerUpcardValue >= 4 && dealerUpcardValue <= 7) ||
          (pairValue === '4' && (dealerUpcardValue === 5 || dealerUpcardValue === 6)) ||
          (pairValue === '6' && dealerUpcardValue >= 3 && dealerUpcardValue <= 6) ||
          (pairValue === '7' && dealerUpcardValue >= 2 && dealerUpcardValue <= 7) ||
          (pairValue === '9' && (dealerUpcardValue >= 2 && dealerUpcardValue <= 6 || dealerUpcardValue === 8 || dealerUpcardValue === 9))) {
        split();
      }
    } else if (isSoftHand) {
      if (playerScore <= 17 ||
          (playerScore >= 13 && playerScore <= 18 && (dealerUpcardValue === 5 || dealerUpcardValue === 6)) ||
          ((playerScore === 19 || playerScore === 20) && dealerUpcardValue === 6)) {
        doubleDown();
      } else if (playerScore >= 19) {
        stand();
      } else {
        hit();
      }
    } else {
      if (playerScore <= 8 ||
          (playerScore === 10 || playerScore === 11 && dealerUpcardValue !== 10 && dealerUpcard !== 'A') ||
          (playerScore >= 12 && playerScore <= 16 && dealerUpcardValue >= 7)) {
        doubleDown();
      } else if (playerScore >= 17) {
        stand();
      } else {
        hit();
      }
    }
  }
</script>

<main class:split={haveSplit}>
  <div id="dealer">
    {#if !gameOver && !hasStood}
      <h2>Dealer: ?</h2>
    {:else}
      <h2>Dealer: {calculateHand(dealerHand)}</h2>
    {/if}
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
  </div>

  <div id="player">
    <h2>Player: {calculateHand(playerHand)}</h2>
    {#if haveSplit}
      <h2>Player Split: {calculateHand(playerSplitHand)}</h2>
    {/if}
    <h2>Money: {playerMoney}</h2>
    <h2>Bet: {betAmount}</h2>
    <div class="hand">
      {#each playerHand as card, i (i)}
        <div in:fly={{ y: 100, duration: 400, delay: i * 100}}>
          <Card {card} />
        </div>
      {/each}
    </div>
  </div>

  <div id="playerSplit">
    <div class="hand">
      {#each playerSplitHand as card, i (i)}
        <div in:fly={{ y: 100, duration: 400, delay: i * 100}}>
          <Card {card} />
        </div>
      {/each}
    </div>
  </div>

  <div id="controls">
    <button on:click={hit}>Hit</button>
    <button on:click={doubleDown} class:disabled={playerHand.length !== 2 || playerMoney < betAmount * 2}>Double Down</button>
    <button on:click={stand}>Stand</button>
    <button on:click={split} class:disabled={!cansplit}>Split</button>
    <button on:click={hitSplit} class:disabled={!haveSplit} disabled={!haveSplit}>Hit Split</button>

    <button on:click={basicStrategy}>Basic Strategy</button>
  </div>

  <div id="betting">
    <input bind:value={betAmount} type="number" min="1" max={playerMoney} placeholder="Enter bet amount" disabled={betPlaced} />
    <button on:click={() => placeBet(betAmount)} disabled={betPlaced}>Place Bet</button>
    {#if error}
      <p>{error}</p>
    {/if}
  </div>

  {#if showOverlay}
      <div class="overlay">
          <h2>Winner: {determineWinner()}</h2>
          {#if haveSplit}
            <h2>Winner Split: {determineWinnerSplit()}</h2>
          {/if}
      </div>
  {/if}

  <Stats {deck} dealerSecondCard={dealerHand[1]} />

</main>

<style>
  main {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
    width: 600px; /* Adjust the size to your preference */
    height: 900px; /* Adjust the size to your preference */
    margin: auto;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    background-color: #f9f9f9;
    transition: height 0.5s ease;
  }

  main.split {
    height: 1200px; /* Increased height */
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
  }

  button:hover {
    background-color: #0056b3;
  }

  #betting {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1em;
  }

  #betting input {
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }

  .disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
</style>