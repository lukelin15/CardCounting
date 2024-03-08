<script>
  import { fly } from 'svelte/transition';
  import Card from './Card.svelte';
  import Stats from './Stats.svelte';
  import Graph from './Graph.svelte';
  import Counting from './Counting.svelte';
  import StatsGraph from './StatsGraph.svelte';

  // customization
  export let stats = false;
  export let simulate = false;

  // deck initializing stuff
  let vals = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
  let suits = ['♠', '♣', '♥', '♦'];
  let newDeck = [];

  let currentMode = 'none'; // This will hold the current mode selected in Counting

  function handleModeChange(event) {
    currentMode = event.detail.mode;
    restart();
  }
  
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
  let runningCount=0;
  let stopSimulation = true;
  let winningCards = [];

  let moneyHistory = [1000];
  export let svgId = "graph";

  // only display overlay after animations
  // equations gets amount of extra cards dealt
  $: if (gameOver) {
    timeoutId = setTimeout(() => {
        showOverlay = true;
    }, (Math.max(1, dealerHand.length -2)) * 100 + 400);
  }

  function restart() {
    deck = [...newDeck]

    stopSimulation = true;

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
    runningCount = 0;

    moneyHistory = [1000];
    winningCards = [];

    shuffle();
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
      runningCount = 0
      shuffle()
    }
      let card = deck.pop();
      calculateRunningCount(card);
      let newHand = [...hand, card];
      deck = [...deck];
      return newHand;
  }

  function doubleDown() {
    if (playerHand.length === 2 && (playerMoney >= betAmount * 2) || simulate) {
      playerMoney -= betAmount;
      betAmount *= 2;
      hit();
      stand();
    }
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

  function smartHit(bet) {
    let dealerCard = String(dealerHand[0]).slice(0,-1);

    // doubling down when simulating
    function fakeDouble() {
      playerMoney -= betAmount;
      betAmount *= 2
    }

    // count aces
    let aces = 0;
    for (let card of playerHand) {
      let value = card.slice(0, -1);
      if (value === 'A') {
        aces += 1;
      }
    }

    // soft responses
    if (aces = 1) {
      if (calculateHand(playerHand) == 13 || calculateHand(playerHand) == 14) {
        if (['5', '6'].includes(dealerCard)) {
          fakeDouble();
          return
        } else {
          playerHand = deal(playerHand);
        }
      } else if (calculateHand(playerHand) == 15 || calculateHand(playerHand) == 16) {
         if (['4', '5', '6'].includes(dealerCard)) {
          fakeDouble();
          return
        } else {
          playerHand = deal(playerHand);
        }
      } else if (calculateHand(playerHand) == 17) {
          if (['3', '4', '5', '6'].includes(dealerCard)) {
            fakeDouble();
            return
          } else {
            playerHand = deal(playerHand);
          }
      } else if (calculateHand(playerHand) == 18) {
          if (['3', '4', '5', '6'].includes(dealerCard)) {
            fakeDouble();
            return
          }
        }
    }

    // always hit when below 11 or below
    while (calculateHand(playerHand) <= 11) {
      playerHand = deal(playerHand);
    }

    // 12 stands on 4 5 6
    if (calculateHand(playerHand) == 12) {
      if (['4', '5', '6'].includes(dealerCard)) {
        return
      } else {
        playerHand = deal(playerHand);
      }
    }

    // 13 14 15 16 stands on 2 3 4 5 6
    if (13 <= calculateHand(playerHand) <= 16) {
      if (['2', '3', '4', '5', '6'].includes(dealerCard)) {
        return
      } else {
        playerHand = deal(playerHand);
      }
    }

    // always stand 17
    if (calculateHand(playerHand) == 17) {
      return
    }

    return
  }

  function placeBet(amount) {
    if (simulate) {
      playerMoney -= amount;
      betAmount = amount;
      newGame();
      betPlaced = true;
      error = ""
    } else if (amount <= 0) {
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
    runningCount = 0;

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
      for (let card of playerHand) {
        winningCards.push(card);
      }
      winningCards = [...winningCards];
    } else if (winner === 'Draw') {
      playerMoney += betAmount; // player gets their bet back
      ties += 1
    } else {
      losses += 1
    }
    betPlaced = false;
    moneyHistory.push(playerMoney);
    moneyHistory = [...moneyHistory];
    return winner;
  }

  async function simulateRounds(rounds, instant=false) {
    stopSimulation = false
    for (let i = 0; i < rounds; i++) {

      if (currentMode == 'none') {
        placeBet(100);
      } else if (currentMode == 'hi-lo' || currentMode == 'halves') {
        if (runningCount >= 2) {
          betAmount = Math.round(playerMoney * (runningCount) / 1000)
          placeBet(betAmount);
        } else {
          betAmount = Math.round(playerMoney / -(runningCount-2) / 1000)
          placeBet(betAmount)
        }
      }

      simulateHit();
      simulateStand();

      determineWinner();

      if (stopSimulation) {
        stopSimulation = false
        break;
      }

      if (!instant) {
        await new Promise(resolve => setTimeout(resolve, 500.0 / rounds));
      }
    }
  }

  function calculateRunningCount(card){
    let val = card.slice(0, -1);
    if (currentMode == 'hi-lo') {
      if (val >=2 && val<=6) {
        runningCount += 1;
      } else if ([10, 'K', 'Q', 'J', 'A'].includes(val)) {
        runningCount -= 1;
      } else {
        runningCount += 0;
      }
    }
    if (currentMode == 'halves') {
      if ([2, 7].includes(val)) {
        runningCount += 0.5;
      } else if ([3, 4, 6].includes(val)) {
        runningCount += 1;
      } else if (val == 5) {
        runningCount += 1.5;
      } else if (val == 9) {
        runningCount -= 0.5
      } else if ([10, 'K', 'Q', 'J', 'A'].includes(val)) {
        runningCount -= 1;
      } else {
        runningCount += 0;
      }
    }
  }
</script>

<main>
  <div class="addons" class:stats-active={stats} class:simulate-active={simulate}>
    {#if stats}
    <Counting on:modeChange={handleModeChange} counts={runningCount} />
    {/if}
    <div class="blackjack-container">
      <div class="header">
        <h1 class="blackjack-header">BLACKJACK</h1>
      </div>

      <div class="mid">
        <div class="top-bar">
          <div class="betting">
            Bet: $
            <input bind:value={betAmount} type="number" min="1" max={playerMoney} placeholder="100" disabled={betPlaced} />
          </div>
          <button on:click={() => placeBet(betAmount)} disabled={betPlaced}>Place</button>
          <button on:click={() => restart()}> Restart</button>
          {#if simulate}
            <button on:click={() => simulateRounds(1000, false)}>Simulate</button>
          {/if}
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
          <div>Games: {wins + losses + ties}</div>
          <div>Wins: {wins}</div>
          <div>Losses: {losses}</div>
          <div>Ties: {ties}</div>
          <div>Money: {playerMoney}</div>
          <h2>Player: {calculateHand(playerHand)}</h2>
        </div>
      </div>
      <div id="controls">
        <button on:click={hit} disabled={gameOver || !hasStarted}>Hit</button>
        <button on:click={stand} disabled={gameOver || !hasStarted}>Stand</button>
        <button on:click={doubleDown} disabled={gameOver || !hasStarted || playerHand.length > 2}>Double</button>
      </div>

      {#if showOverlay}
          <div class="overlay">
              <h2>Winner: {determineWinner()}</h2>
              <!-- <div>{betAmount}</div> -->
          </div>
      {/if}
      {#if simulate}
        <Graph playerMoneyHistory={moneyHistory} svgId={svgId}/>
      {/if}
    </div>
    {#if stats}
    <div class="stats" class:simulate-active={simulate}>
      <Stats {deck} dealerSecondCard={dealerHand[1]} playerHand={playerHand} simulate={simulate}/>
      {#if simulate}
        <StatsGraph {winningCards} />
      {/if}
    </div>
    {/if}
  </div>
</main>

<style>
  
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .addons {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 600px;
    height: 400px;
    transition: width 0.5s ease, height 0.5s ease;
    background: white;
    border: 2px solid black;
    padding: 20px;
    border-radius: 20px;
  }

  .addons.stats-active {
    width: 1200px; /* Expanded width */
  }

  .addons.simulate-active {
    height: 700px; /* Adjusted height for simulate, set to your preferred height */
  }

  .blackjack-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%;
    max-width: 600px;
    background-color: white;
    border-radius: 20px;
  }

  .stats {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .stats.simulate-active {
    height: 700px;
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
    overflow: visible;
    width: 100%;
    min-height: 140px;
  }

  .overlay {
    flex-direction: column;
    position: absolute;
    width: 20%;
    height: 10%;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgba(0, 0, 0, 0.5);
    border-radius: 20px;
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

  button:disabled {
    background-color: lightgray;
  }
</style>