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
  <body>
  <p>
    Have you ever wondered why some people seem to have an uncanny knack for winning at card games? Is it all just down to luck, or is there more to it? Welcome to our interactive exploration of Blackjack, a game where strategy can tip the scales in your favor.
  </p>
  <p>
    In its simplest form, Blackjack is a card game where players attempt to reach a score of 21 by summing the values of their dealt cards. But beneath this simplicity lies a world of strategy and calculated risk. Let's dive in and uncover the secrets of this popular game.
  </p>
  </body>
  <Base />
  <body>
    <h2>The Art of Card Counting</h2>
    <p>
      It's easy to think that winning at Blackjack is just a matter of luck. But what if we told you that there's a way to tilt the odds in your favor? Enter the world of card counting.
    </p>
    <p>
      Card counting is a strategy used by Blackjack players to keep track of the ratio of high to low value cards left in the deck. It's not about memorizing every card that's been dealt, but rather understanding the overall composition of the deck. And while it's not a guarantee of winning, it can certainly give you an edge.
    </p>
  </body>
  <Base stats=true/>
  <h2>Simulating the Game</h2>
  <body>
    <p>
      Now that we've introduced the concept of card counting, let's put it to the test. We've created a simulation of a Blackjack game that allows you to try out this strategy for yourself. Will it make a difference? There's only one way to find out.
    </p>
  </body>
  <Base stats=true simulate=true/>
  <h2>The Thrill of Betting</h2>
  <h2>Unraveling the Statistics</h2>
  <h2>Mastering Card Counting</h2>
  <body>
    <p>
      As we've seen, card counting is a powerful tool in the hands of a skilled Blackjack player. But it's not just about keeping track of the numbers. It's about understanding the game, reading the situation, and making smart decisions. It's about turning a game of chance into a game of strategy.
    </p>
    <p>
      So, are you ready to take your Blackjack game to the next level? With our interactive guide, you can learn the ins and outs of card counting and see how it can transform your approach to the game. It's not just about winning - it's about understanding the game on a deeper level.
    </p>
  </body>
  <Base stats=true simulate=true counts=true svgId="graph2"/>
</main>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400..700;1,400..700&family=PT+Serif:ital,wght@0,400;0,700;1,400;1,700&display=swap');
  @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100;300;400;500;700;900&display=swap');
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@900&display=swap');

  .header {
    background-color: #d2b48c; 
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
    margin: 0 auto;
    width: 80%;
    text-indent: 30px;
    font-family: "Roboto", serif;
  }
</style>
