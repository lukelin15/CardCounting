<script>
  import BlackJack from './Blackjack.svelte';
  import Base from './Base.svelte'
  import BaseCounts from './BaseCounts.svelte';
  import BasicStrategy from './BasicStrategy.svelte';
  // Write your JS here, or import other files

  // presentation stuff
  let stats = false;
  let simulate = false;

  function statsButton() {
    stats = !stats;
  }
  function simultateButton() {
    simulate = !simulate;
  }
</script>


<main>
  <div class="header">
    <h1>Unveiling Blackjack</h1>
    <h2>a game of strategy, not just luck</h2>
    <h4>by Andrew, Luke, and Pallavi</h4>
  </div>
  <h1>UNVEILING BLACKJACK</h1>
  <h2>A GAME OF STRATEGY, NOT JUST LUCK</h2>
  <h3>BY ANDREW, LUKE, AND PALLAVI</h3>
  <Base stats={stats} simulate={simulate}/>
  <button on:click={statsButton} style = "margin: auto; display: block;">stats</button>
  <button on:click={simultateButton} style = "margin: auto; display: block;">simulate</button>


  <!-- makes Base the only visible thing -->
  <div style="height: 50px;"></div>
  <h2>Introduction to Blackjack</h2>
  <body>
  <p>
    BlackJack, in it's most simplist form, is a card game wherein players attemp to reach a score of 21 by summing 
    the values of their dealt cards. Aces can count for either a 1 or a 11 and face cards (K, Q, J) count
    as 10. Players play against eachother and the dealer. You win by getting a score of 21 with the least amount of cards or 
    getting the highest score under 21. Players automatically lose of their hand is above 21. Players can choose 'Hit' to
    take another card to increase the total value of their cards or to 'Stand' wherein you keep 
    your current hand without taking any additional cards. 'Double' refers to doubling your original bet and 
    receiving exactly one more card.
  </p>
  <p>
    Try playing a round!
  </p>
  </body>
  <Base />
  <body>
    <div class="header">
      <h2>Part 1:</h2>
      <h4>Why Card Count?</h4>
    </div>
    <div style="height: 50px;"></div>
    <h2>Visualizing the Cards</h2>
    <p>
      It might seem like winning and losing is just random luck, up to chance of the cards you
      end up being dealt. However, if you know what cards are left in the deck, you can actually 
      see your odds of obtaining a desirable card.
    </p>
    <p>
      Now you have a visualization of all the cards in the deck. Cards that have been dealt and 
      are no longer in the deck are shaded in. Try playing a round and see if this helps you 
      in your game decisions.
    </p>
    <div style="height: 25px;"></div>
  </body>
  <Base stats=true/>
  <div style="height: 50px;"></div>
  <h2>Statistics and Simulating a Game</h2>
  <body>
    <p>
      You may have noticed that seeing all the cards doesn't really help all that much. Because while 
      you can now see all the cards, unless you are calculating the exact probability of getting a desirable card 
      between every round, seeing so many cards does not really help most people. The odds
      in BlackJack are as follows: 
    </p>
    <div style="height: 25px;"></div>
    <p>
      This is done my calculating the probability of a player getting a card that will cause their
      total go go over 21 based on their current total of their first two cards. Naturally, as 
      cards are dealt these odds change and less and less cards remain in the deck.
    </p>
    <p>
      Simulate a game below with No Card Counting to see how Player money rises and falls when a 
      player blindly plays against the dealer.
    </p>
    <div style="height: 25px;"></div>
  </body>
  <Base stats=true simulate=true/>
  <div class="header">
    <h2>Part 2:</h2>
    <h4>Mastering Card Counting</h4>
  </div>
  <body>
    <p>
      As displayed in the "Visualizing the Cards" section above, keeping track of every single card is simply 
      not feasible since there are 52 cards in a deck, its more unrealistic to calculate 
      the exact odds of busting as the games goes on. Since a typical game of BlackJack can 
      have 6-8 decks in play, this becomes impossible. Thus, players use card counting to
      keep track of cards.
    </p>
    <p>
      A simple form of Card Counting is the Hi-Lo system. As the name suggests, it 
      keeps track of the ratio of high valued cards vs low values cards left in the deck.
      Cards valued 2-6 are worth +1, cards valued 7-9 are worth 0, and 10 to Ace is worth. 
      As each card is dealt add the appropriate amount to your running count until all the cards in the deck are dealt.
      As the sunning count increasing, the advantage turns towards the player. But if this becomes negative,
      the dealer's advantage increases.
    </p>
    <p>
      Another form of card counting is the Halves system which is more complicated than the 
      aforementioned Hi-Lo system. Like Hi-Lo, cards are assigned different values to add to your 
      running count. But this time, a 5 is worth +1.5; 3, 4 and 6 are worth +1; 2 and 7 are worth 
      +0.5, 8 is worth 0, 9 is worth -0.5, and 10, J, Q, K, and A are worth -1. This system 
      is proven to give more accurate information than the Hi-Lo system, but these point values 
      are harder to calculate. Like the Hi-Lo system, the running count helps players keep 
      track of the ratio of low cards and high cards.
    </p>
    <p>
      Click on the simulate button with the different card counting strategies to see how the running count changes
      and how that can inform the bets you make in comparision to not card counting at all.
    </p>
    <div style="height: 25px;"></div>
  </body>
  <Base stats=true simulate=true counts=true svgId="graph2"/>
  <body>
  <div class="header">
    <h2>Part 3:</h2>
    <h4>The Statistics</h4>
  </div>
    <div style="height: 25px;"></div>
    <p>
    As the simulation demonstrates, while using these counting systems do not necessarily help players
    make a lot of profit, they to inform bets and prevent players from losing money. The 
    graph illustrating player money over bets visualizes this. Players tend to lose a lot of money
    and even go into debt very quickly when betting and playing blind against the dealer. However, when
    informing bets based on the different card counting systems, players are able to remain fairly steady. 
    </p>
</body>
<body>
  <div style="height: 50px;"></div>
  <h2>Try it yourself!</h2>
  <p>
    Try using what you have learned about card counting and play a couple rounds. We have
    hidden the statistics from you, but feel free to turn them on by clicking the button if you are 
    feeling a little stuck.
  </p>
  <div style="height: 25px;"></div>
</body>
<Base stats={stats} svgId="graph2"/>
  <button on:click={statsButton} style = "margin: auto; display: block;">stats</button>
  <div style="height: 50px;"></div>
<!-- <BasicStrategy /> -->
<h2>Conclusion and Takeaways</h2>
<body>
  <p>
    Ultimately, anyone can learn Blackjack and Card Counting. And while this may not be applicable 
    in everyday life, statistics and strategies like card counting - which is simply keeping track
    of the ratio of high valued items vs low valued items - certainly can be.
  </p>
  <p>
    Ultimately, our visualization breaks down statistics and game theory 
    into digestible steps and logic for the everyday user to understand 
    with lessons in betting and card counting. Our visualization is able 
    to do this effectively but using a combination of simulations, text, and 
    interactive visuals to communicate to users both visually and kinesthetically.
  </p>
  <p>
    One thing people should learn from our visualization is that statistics does not 
    have to be super complicated. Anyone can learn how to card count, or even apply 
    statistics in their daily life whether that be with fitness tracking, 
    consumerism/price tracking, social media trends, or even personal finance. Fundementally,
    our visualization aims to demystify the confusion around math and statistics 
    as card counting at the fundamental level is simply keeping tracking of the 
    ratio of low items to high items. 
  </p>
  <p>
    Our visualization succeeds at explaining this by going through the process of 
    betting and card counting step by step. We only add one new piece of 
    information at a time and use simulations and visuals to aid the text 
    explanation. Our hope is that a combination of these elements helps users 
    fundamentally understand the mechanics behind not only betting and card 
    counting in this blackjack application, but math and statistics at the higher 
    level.  
  </p>
</body>
</main>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400..700;1,400..700&family=PT+Serif:ital,wght@0,400;0,700;1,400;1,700&display=swap');
  @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100;300;400;500;700;900&display=swap');
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@900&display=swap');

  .header {
    background-color: #d4c8b9; 
    padding: 40px 0;
    text-align: center;
    /* background-image: url('https://images.unsplash.com/photo-1541278107931-e006523892df?q=80&w=2071&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D'); Replace with your cards image URL */
    background-repeat: no-repeat;
    background-size: cover;
    color: white;
  }

  .header h1 {
    font-family: 'Montserrat', sans-serif;
    font-size: 4.5em;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    margin-bottom: 0.2em;
  }

  .header h2 {
    font-family: 'Montserrat', sans-serif;
    font-size: 2em;
    text-transform: uppercase;
    font-weight: normal; 
    letter-spacing: 0.05em; 
    color: white;
  }
  .header h4 {
    font-family: 'Montserrat', sans-serif;
    font-size: 1em;
    text-transform: uppercase;
    font-weight: normal;
    letter-spacing: 0.05em; 
    color: white;
  }

  body {
    margin: 0;
    padding: 0;
    /* background-image: url('https://st.depositphotos.com/3215383/5028/i/450/depositphotos_50286385-stock-photo-nice-abstract-background.jpg'); */
    background-repeat: no-repeat;
    background-attachment: fixed;
    background-size: cover;
    color: #333;
  }

  p {
    font-size: 1.2em;
    line-height: 1.15;
    margin:  40px;
    width: 80%;
    text-indent: 30px;
    font-family: "Roboto", serif;
  }
</style>

