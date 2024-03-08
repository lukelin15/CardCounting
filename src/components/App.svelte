<script>
  import BlackJack from './Blackjack.svelte';
  import Base from './Base.svelte'
  import BasicStrategy from './BasicStrategy.svelte';
  // Write your JS here, or import other files
</script>

<main>
  <h1>Card Interaction and Counting: BlackJack</h1>
  <Base />
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
<!-- <BasicStrategy /> -->
</main>

<style>
  h1 {
    text-align: center;
  }
  body {
    /* background-image: url('https://st.depositphotos.com/3215383/5028/i/450/depositphotos_50286385-stock-photo-nice-abstract-background.jpg'); */
    background-repeat: no-repeat;
    background-attachment: fixed;
    background-size: cover;
    color: #333;
    margin: 10;
    padding: 20px;
  }
  p {
    font-size: 1.2em;
    line-height: 1.15;
    margin: 0 auto;
    width: 80%;
    text-indent: 30px;
  }
</style>
