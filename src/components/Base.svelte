<script>
  import { fly } from 'svelte/transition';
  import Card from './Card.svelte';
  import Stats from './Stats.svelte';
  import { onMount, afterUpdate } from 'svelte';
  import * as d3 from 'd3';

  // customization
  export let stats = false;
  export let simulate = false;
  export let counts = false;

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
  let runningCount=0;
  let stopSimulation = true;

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
    runningCount = 0;
    stopSimulation = true;
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
      betAmount *= 2;
      playerMoney -= betAmount;
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

  function smartHit() {
    let dealerCard = String(dealerHand[0]).slice(0,-1)

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
    } else if (winner === 'Draw') {
      playerMoney += betAmount; // player gets their bet back
      ties += 1
    } else {
      losses += 1
    }
    betPlaced = false;
    return winner;
  }

  async function simulateRounds(rounds, instant=false, counting=false) {
    stopSimulation = false
    console.log(5000.0 / rounds)
    for (let i = 0; i < rounds; i++) {

      if (!counting) {
        placeBet(100);
      } else {
        if (runningCount >= 3) {
          betAmount = Math.round(playerMoney * (runningCount-1) / 1000)
          placeBet(betAmount);
        } else {
          betAmount = Math.round(playerMoney / -(runningCount-3) / 1000)
          console.log(betAmount)
          placeBet(betAmount)
        }
        // smartHit();
      }

      simulateHit();
      simulateStand();

      determineWinner();

      if (stopSimulation) {
        stopSimulation = false
        break;
      }

      if (!instant) {
        await new Promise(resolve => setTimeout(resolve, 5000.0 / rounds));
      }
    }
  }

  function calculateRunningCount(card){
    let val = card.slice(0, -1);
    if (val >=2 && val<=6) {
      runningCount += 1;
    } else if ([10, 'K', 'Q', 'J', 'A'].includes(val)) {
      runningCount -= 1;
    } else {
      runningCount += 0;
    }
  }


  let playerMoneyHistory = [];

  // Update playerMoneyHistory every time playerMoney changes
  $: playerMoneyHistory.push(playerMoney);

  let svg;
  

  onMount(() => {
    if ((stats && simulate) || counts) {
      svg = d3.select("#mySvg");
    }
  });

  afterUpdate(() => {
    if ((stats && simulate) || counts && svg) {
      drawChart();
    }
  });

  function drawChart() {
    // Clear the SVG
    svg.selectAll("*").remove();

    // Set the dimensions and margins of the graph
    let margin = {top: 10, right: 30, bottom: 30, left: 60},
        width = 460 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

    // Add X axis
    let x = d3.scaleLinear()
      .domain([0, playerMoneyHistory.length])
      .range([ 0, width ]);
    svg.append("g")
      .attr("transform", "translate(0," + height + ")")
      .call(d3.axisBottom(x));

    // Add Y axis
    let y = d3.scaleLinear()
      .domain([0, d3.max(playerMoneyHistory)])
      .range([ height, 0]);
    svg.append("g")
      .call(d3.axisLeft(y));

    // Add the line
    svg.append("path")
      .datum(playerMoneyHistory)
      .attr("fill", "none")
      .attr("stroke", "steelblue")
      .attr("stroke-width", 1.5)
      .attr("d", d3.line()
        .x((d, i) => x(i))
        .y(d => y(d))
      );
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
          Bet: $
          <input bind:value={betAmount} type="number" min="1" max={playerMoney} placeholder="100" disabled={betPlaced} />
        </div>
        <button on:click={() => placeBet(betAmount)} disabled={betPlaced}>Place</button>
        <button on:click={() => restart()}> Restart</button>
        {#if simulate}
          <button on:click={() => simulateRounds(10000, true, true)}>Simulate</button>
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
        <div>Wins: {wins}</div>
        <div>Losses: {losses}</div>
        <div>Ties: {ties}</div>
        <div>Money: {playerMoney}</div>
        {#if counts}
          <div>Running Count: {runningCount}</div>
        {/if}
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
        </div>
    {/if}


    {#if stats}
      <Stats {deck} dealerSecondCard={dealerHand[1]} playerHand={playerHand}/>
    {/if}
  </div>
  {#if (stats && simulate) || counts}
    <svg id="mySvg" width="500" height="500"></svg>
  {/if}
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 10px;
  }

  .blackjack-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%; /* Set this to match the container size you want */
    max-width: 600px; /* As per the image's apparent width */
    background: white;
    border-radius: 20px;
    border: 2px solid black;
    padding: 20px;
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
    width: 25%;
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

  button:disabled {
    background-color: lightgray;
  }
</style>