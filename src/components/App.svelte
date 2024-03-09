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
  <h1>Card Interaction and Counting: BlackJack</h1>
  <Base stats={stats} simulate={simulate}/>
  <button on:click={statsButton}>stats</button>
  <button on:click={simultateButton}>simulate</button>


  <!-- makes Base the only visible thing -->
  <div style="height: 100px;"></div>
  <h2>Introduction to Blackjack</h2>
  <body>
  <p>
    BlackJack, in it's most simplist form, is a card game wherein players attemp to reach a score of 21 by summing 
    the values of their dealt cards. Aces can count for either a 1 or a 11 and face cards (K, Q, J) count
    as 10. Players play against eachother and the dealer. You win by getting a score of 21 with the least amount of cards or 
    getting the highest score under 21. Players automatically lose of their hand is above 21. Players can choose 'Hit' to
    take another card to increase the total value of their cards or to 'Stand' wherein you keep 
    your current hand without taking any additional cards.
  </p>
  <p>
    Try playing a round!
  </p>
  </body>
  <Base />
  <body>
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
  </body>
  <Base stats=true/>
  <h2>Simulating a Game</h2>
  <body>
    <p>
      You may have noticed that seeing all the cards doesn't really help all that much. The odds
      in BlackJack are as follows: 
    </p>
    <p style="color: white;">
      space
    </p>
      <p>
      <img src="src/components/odds.png" alt="odds chart">
    </p>
    <p style="color: white;">
      space
    </p>
    <p>
      This is done my calculating the probability of a player getting a card that will cause their
      total go go over 21 based on their current total of their first two cards. Naturally, as 
      cards are dealt these odds change and less and less cards remain in the deck.
    </p>
    <p>
      Simulate a game below with No Card Counting to see how Player money rises and falls.
    </p>
  </body>
  <Base stats=true simulate=true/>
  <h2>Card Counting</h2>
  <body>
    <p>
      As displayed in the "Visualizing the Cards" section above, keeping track of everys single card is simply 
      not feasible since there are 52 cards in a deck, and its not feasible to calculate 
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
    <p style="color: white;">
      space
    </p>
      <p>
      <img src="src/components/hilo.png" alt="hilo chart">
    </p>
    <p style="color: white;">
      space
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
    <p style="color: white;">
      space
    </p>
      <p>
      <img src="src/components/halves.png" alt="halves chart">
    </p>
    <p style="color: white;">
      space
    </p>
  </body>
  <Base stats=true simulate=true counts=true svgId="graph2"/>
  <body>
    <p>
    As the simulation demonstrates, while using these counting systems do not necessarily help players
    make a lot of profit, they to inform bets and prevent players from losing money. The 
    graph illustrating player money over bets visualizes this. Players tend to lose a lot of money
    and even go into debt very quickly when betting and playing blind against the dealer. However, when
    informing bets based on the different card counting systems, players are able to reamin fairly steady. 
    </p>
  </body>
  <h2>Conclusion</h2>
  <body>
    <p>
      Ultimately, anyone can learn Blackjack and Card Counting. And while this may not be applicable 
      in everyday life, statistics and strategies like card counting - which is simply keeping track
      of the ratio of high valued items vs low valued items certainly can be.
    </p>
  </body>
  <!--
  <body>
  <p>
    1. Thus far, we have created a working single-player BlackJack game. In this game, 
    two cards are dealt, two for both the dealer and the player. However, one of the dealer's cards are hidden.
    Then the player has the choice to 'hit' or 'stand' based on the score of their cards. In the end, a
    card pops up to display the winner of the game: either the player or the dealer. Players can then start a 
    new game. We have also included a visualization of all the cards below to show which cards have been used and
    discarded and which are still in the deck: cards still in the deck are at 100% opacity while cards that are out are shaded. 
    After all cards have been dealt, the deck is shuffled and the game is reset.
  </p>
  <p>
    2. We think the most challenging part of the design will be having this game tell a story. As of right now, we 
    just have a working BlackJack game and visualization of all of the cards in the deck, but we hope that we can explain 
    the system of card counting and/or helping players understand their odds while playing games as it's not just
    luck, there is stategy and game theory involved in games like BlackJack to maximize profit. 
    Thus, we want to create some sort of story/interactive presentation to teach users card counting, namely
    the Hi-Lo card counting system wherein you assign point values to the cards dealt in order
    to keep track of the ratio of high cards to low cards in the deck. Right now, we just have the game and visualization of all the cards. Then, in the end, they will hopefully have the chance to play their own
    game applying this stategy that they have learned. We want to add a tally of games the dealer has won vs the player and hopefully, 
    with the card counting stategy, the player ends up winning a majority of the games. Moreover, we may also try to implement more customization
    for the user to play around with like increasing the number of decks (as a standard game of BlackJack has 6 or 8 decks) and/or choosing 
    the number of players. However, we think these customizations may be the hardest part of this project as it gives the user complete control
    over their game and so we have to adapt our game to handle that. As an overall scheme of our final project, we may have the user play one initial game of BlackJack, then introduce
    the stategy of card counting through a visual presentation of sorts, then end the article with BlackJack with more customizations so users
    can choose their experience and apply what they have learned about card counting to increase their amount of wins.
  </p>
</body>
-->
<!-- <BasicStrategy /> -->
</main>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400..700;1,400..700&family=PT+Serif:ital,wght@0,400;0,700;1,400;1,700&display=swap');

  h1, h2 {
    text-align: center;
    font-family: "PT Serif", serif;
  }
  body {
    margin: 20px;
    padding: 5px;
  }

  p {
    font-size: 1.2em;
    line-height: 1.15;
    margin: 0 auto;
    width: 80%;
    text-indent: 30px;
    font-family: "PT Serif", serif;
  }

  img {
      width: 300px; 
      height: auto; 
      display: block; 
      margin: 0 auto; 
      border: 2px solid #333; 
    }
</style>
