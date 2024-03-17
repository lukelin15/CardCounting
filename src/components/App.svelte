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
  <div class="header" style="margin-top: 0px;">
    <h1>Unveiling Blackjack</h1>
    <h2>a game of strategy, not just luck</h2>
    <h4>by Andrew, Luke, and Pallavi</h4>
    <a href="https://youtu.be/JtHIpce4yZk">Demo Video</a>
  </div>
  <body>
  <p>
    Have you ever wondered why some people seem to have an uncanny knack for winning at card games?
    Is it all just down to <i>luck</i>, or is there more to it? 
    We will explore that today in our interactive exploration of <b>Blackjack</b>, 
    a game where good strategy along with some useful math can help tip the scales in your favor.
  </p>
  <p>
    BlackJack, in it's most simplist form, is a <i>card</i> game wherein players attempt to reach a score of at most 21 by summing 
    the values of their dealt cards. Aces can count for either a 1 or a 11 and face cards (K, Q, J) count
    as 10. Players play against the dealer. You win by getting a score higher than the dealer without going over 21 or you win if the dealer went over 21.
    Players and dealers automatically lose if their hand's value is above 21. Players can choose <b>Hit</b> to
    take another card to increase the total value of their cards or to <b>Stand</b> wherein you keep 
    your current hand without taking any additional cards. <b>Double</b> refers to doubling your original bet and 
    receiving exactly one more card. This can only be done as your first and only move.
  </p>
  <p>
    <b>Try playing a couple rounds!</b>
  </p>
  <Base />
  <p>
    You might have noticed if you played enough rounds that you consistently <i>lose</i> money.
    That shouldn't make sense right, the dealer and the player both get the same opportunities.
    They follow the same rules and bet the same amount of money.
    The reason the player loses more often is that they must go <i>first</i>.
  </p>
  <p>
    When the player goes first, they face the risk of <b>busting</b>, which means their hand 
    total exceeds 21. This instantly results in a loss for the player, regardless of the 
    dealer's hand. This element of risk introduces an asymmetry in the game dynamics, as 
    the dealer doesn't need to take additional cards if the player busts, automatically 
    winning the round.
  </p>
  <p>
    This is why BlackJack is offered so much at casinos.
    The player is at a heavy disadvantage from the start. 
    In fact, the player playing the same as the dealer only has around a <i>40%</i> chance to win because they have to act first.
    There are however, things you can do to change this.
  </p>
  <p>
    <b>Lets learn about card counting.</b>
  </p>
  </body>
  <body>
    <div class="header">
      <h2>Part 1:</h2>
      <h4>Why Card Count?</h4>
      <h4>Visualizing the Cards</h4>
    </div>
    <p>
      It might seem like winning and losing is just random luck. 
      You might be worried that the chance of getting the cards you want is completely arbitrary. 
      However, if you know what cards are left in the deck, you can actually 
      utilize this to find the odds of getting a <i>desirable</i> card.
    </p>
    <p>
      Now you have a <b>visualization</b> of all the cards in the deck. Cards that have been dealt and 
      are no longer in the deck are shaded in. Try playing a round and see if this helps you 
      in your game decisions.
    </p>
    <Base stats=true countMode=1/>
    <p>
      You may have noticed that seeing all the cards <b>doesn't</b> actually help as much as you might've hoped. 
      This is because while you can now see all the cards, unless you are calculating the <i>exact</i> probability of getting a desirable card 
      between every round, seeing so many cards does not really help most people. 
      With a <i>full</i> deck the odds of BlackJack are as follows: 
    </p>
    <table border="1">
      <thead>
        <tr>
          <th colspan="2" style='background-color: #d4c8b9'>BlackJack Odds</th>
      </tr>
          <tr>
              <th>Player Hand Value</th>
              <th>Probability of Busting</th>
          </tr>
      </thead>
      <tbody>
          <tr>
              <td>11 or lower</td>
              <td>0%</td>
          </tr>
          <tr>
            <td>12</td>
            <td>31%</td>
          </tr>
          <tr>
            <td>13</td>
            <td>39%</td>
          </tr>
          <tr>
            <td>14</td>
            <td>56%</td>
          </tr>
          <tr>
            <td>15</td>
            <td>58%</td>
          </tr>
          <tr>
            <td>16</td>
            <td>62%</td>
          </tr>
          <tr>
            <td>17</td>
            <td>69%</td>
          </tr>
          <tr>
            <td>18</td>
            <td>77%</td>
          </tr>
          <tr>
            <td>19</td>
            <td>85%</td>
          </tr>
          <tr>
            <td>20</td>
            <td>92%</td>
          </tr>
          <tr>
            <td>21</td>
            <td>100%</td>
          </tr>
      </tbody>
      </table>
    <p>
      This is determined by calculating the probability of a player getting a card that will cause their
      total go go over 21 based on the current total of their first two cards. Naturally, as 
      cards are dealt these odds <i>change</i> and less and less cards remain in the deck.
    </p>
    <p>
      Simulate a game below with the new <b>simulate</b> button to see how Player money rises and falls when a 
      player blindly plays against the dealer.
      Each simulation proceeds with 500 plays where the player operates the same as the dealer.
    </p>
    <Base simulate=true/>
    <p>
      As the simulation progresses, you'll notice a trend - the player's money tends to decrease drastically
      over time. This is because, in the long run, the <b>house</b> (or the dealer) always has an edge. Without card 
      counting or a solid strategy, a player is likely to lose more than they win.
    </p>
    <p>
      <b>Now we're ready to learn how to card count.</b>
    </p>
  </body>
  <div class="header">
    <h2>Part 2:</h2>
    <h4>Mastering Card Counting</h4>
  </div>
  <body>
    <p>
      As displayed in the <b>Why Card Count?</b> section above, keeping track of every single card is simply 
      not feasible since there are 52 cards in a deck, it becomes more unrealistic to calculate 
      the exact odds of cards you want as the games goes on. Since a typical game of BlackJack at a casino can 
      have 6-8 decks in play, this becomes impossible. Thus, players use card counting to
      keep track of cards.
    </p>
    <p>
      <b>But how do I count?</b>
    </p>
    <p>
      A simple form of card counting we can use is the <b>Hi-Lo</b> system. As the name suggests, it 
      keeps track of the ratio of high valued cards vs low values cards left in the deck.
      Cards valued 2-6 are worth <b>+1</b>, cards valued 7-9 are worth <b>0</b>, and 10 to Ace is worth <b>-1</b>. 
      Start with a count of 0 and as each card is dealt add the appropriate amount to your running count until all the cards in the deck are dealt.
      As the running count increasing, the advantage turns towards the player. But if this becomes negative,
      the dealer's advantage increases. Make sure to reset your count when the deck is shuffled (although if you counted properly you won't need to). 
    </p>
    <table border="1">
      <thead>
        <tr>
          <th colspan="2" style='background-color: #d4c8b9'>Hi-Lo Card Counting</th>
      </tr>
          <tr>
              <th>Card</th>
              <th>Value</th>
          </tr>
      </thead>
      <tbody>
          <tr>
              <td>2-6</td>
              <td>+1.0</td>
          </tr>
          <tr>
            <td>7-9</td>
            <td>+0</td>
        </tr>
        <tr>
          <td>10, J, Q, K, A</td>
          <td>-1.0</td>
      </tr>
      </tbody>
      </table>
    <p>
      <b>Is that all?</b>
    </p>
    <p>
      Another <b>less</b> used form of card counting is the <b>Halves</b> system which is more <i>complicated</i> than the 
      aforementioned Hi-Lo system. Like Hi-Lo, cards are assigned <i>different</i> values to add to your 
      running count. But this time, a 5 is worth <b>+1.5</b>; 3, 4 and 6 are worth <b>+1</b>; 2 and 7 are worth 
      <b>+0.5</b>, 8 is worth <b>0</b>, 9 is worth <b>-0.5</b>, and 10, J, Q, K, and A are worth <b>-1</b>. This system 
      is proven to give more <b>accurate</b> information than the Hi-Lo system, but these point values 
      are <i>harder</i> to calculate. Like the Hi-Lo system, the running count helps players keep 
      track of the <i>ratio</i> of low cards and high cards.
    </p>
    <table border="1">
      <thead>
        <tr>
          <th colspan="2" style='background-color: #d4c8b9'>Halves Card Counting</th>
      </tr>
          <tr>
              <th>Card</th>
              <th>Value</th>
          </tr>
      </thead>
      <tbody>
          <tr>
              <td>5</td>
              <td>+1.5</td>
          </tr>
          <tr>
            <td>3, 4, 6</td>
            <td>+1.0</td>
        </tr>
        <tr>
          <td>2, 7</td>
          <td>+0.5</td>
        </tr>
        <tr>
          <td>8</td>
          <td>+0</td>
        </tr>
        <tr>
          <td>9</td>
          <td>-0.5</td>
        </tr>
        <tr>
          <td>10, J, Q, K, A</td>
          <td>-1.0</td>
        </tr>
      </tbody>
      </table>
    <p>
      Try using the basic Hi-Lo system to count your cards.
      To bring up the table in-game, press the <b>Hi-Lo</b> button on the left.
      Don't worry about winning or losing yet, just try to get the hang of counting the cards.
    </p>
    <Base stats=true counts=true countMode=2/>
    <p>
      You might be wondering how to properly use your running count to beat the dealer.
      The truth is we don't use it for winning <i>individual</i> games.
      The count just tells us how likely we are to win the game <i>before</i> it even starts.
      You might notice the <b>recommended</b> bet goes up as the count goes up and goes down as the count goes down.
      This mirrors the <i>proper</i> way to use your count.
      The amount you want the bet to be is up to you, but as the count gets higher and higher, the odds of you winning increase too.
    </p>
    <p>
      <b>Now we can move on to the math behind this.</b>
    </p>
  </body>
  <body>
  <div class="header">
    <h2>Part 3:</h2>
    <h4>The Statistics</h4>
  </div>
  <p>
    You might be wondering why the count works the way it does.
    We could tell you, but instead we will <i>show</i> you. 
    Using our <b>simulate</b> button simulate another 500 games and focus on the graph in the <b>bottom right</b>.
    It shows you what <i>cards</i> you have on you when you beat the dealer.
    Keep track of which ones are more frequent.
  </p>
  <Base stats=true counts=true simulate=true countMode=1/>
  <p>
    If everything goes right, you should've noticed a huge quantity of 4 specific cards:
    <b>A, 10, J, Q, K</b>. This is why card counting systems evalute them with <b>low</b> points.
    You're much <i>more</i> likely to win with these in the deck, so the <i>more</i> you see, the <i>less</i> likely you win after.
  </p>
  <p>
    Other cards also have <i>distinct</i> probabilities of benefitting you, which is why they aren't all -1 or 0. 
    Cards like <b>7, 8, 9</b> are more neutral which is why they don't affect the count, while <b>lower</b> number cards are bad for the player, so seeing more means <i>better</i> odds later.
  </p>
  <p>
    <b>What about the halves system?</b>
  </p>
  <p>
    It also uses the same principal, the cards are just divided up into even <i>further</i> categories of importance.
    Statistically speaking, havles <i>should</i> be better for your odds of making money, but counting cards with too much variation can often be challenging in a real world scenario.
    This is why the <b>Hi-Lo</b> method is still the most common even though it is a bit worse.
  </p>
  <p>
    <b>But how much worse?</b>
  </p>
  <p>
    Below we have finally unlocked the <b>full</b> extent of the simulation.
    Try simulating <i>both</i> counting methods and comparing them.
    You'll also notice in the <b>bottom left</b> a graph for the running count.
    This shows you the <i>odds</i> of winning with each count.
  </p>
  <p>Experiment a little, <b>go crazy</b>!</p>
  <Base stats=true counts=true simulate=true svgId="graph2"/>
  <p>
  You might've noticed if you did enough attempts that you often still <b>lose</b> money. 
  It's definitely a lot <i>less</i> than before, but it still hasn't allowed you win <b>much</b> yet.
  The simulation's goal is to demonstrate that using these counting systems do not necessarily help players
  make a <i>lot</i> of profit, but instead help prevent players from <b>losing</b> alot of money. When
  informing bets based on the different card counting systems, players are able to remain fairly steady. 
  </p>
  <p>
    <b>Then what's the point of card counting?</b>
  </p>
  <p>
    You might remember from the beginning that our simulation involves the dealer and the player acting the <b>same</b>.
    This is done by hitting when below <b>17</b>. 
    This is traditionally how dealers play in casinos, but player aren't bound by <i>any</i> rules.
    In fact, you can probably find many more guides on when to <b>hit or stand</b> when compared to how to card count!
    Blackjack is a complex game, and while card counting might help you <i>minimize</i> your losses, it's often not enough <b>alone</b> to help you win big money.
    Card counting is only a small fraction of what makes a good Blackjack player, but utlizing every tool to your advantage will allow you to come away with <i>consistent</i> money.
  </p>
  <p>
    <b>Onto the conclusion.</b>
  </p>
</body>
<div class="header">
  <h2>Part 4:</h2>
  <h4>Conclusion and Takeaways</h4>
</div>
<body>
  <p>
    Ultimately, <b>anyone</b> can learn Blackjack and card counting. 
    And while card counting <i>itself</i> may not be applicable 
    in everyday life, <b>statistics and strategies</b> like card counting - which can involve simply keeping track
    of the ratio of high valued items vs low valued items - certainly can be.
  </p>
  <p>
    The goal of our <b>visualization</b> was to break down the statistics and game theory 
    into digestible steps and logic for the <i>everyday</i> user to understand 
    with lessons in betting and card counting. Our visualization is able 
    to do this <i>effectively</i> by using a combination of simulations, text, and 
    interactive visuals to communicate to users both <b>visually and kinesthetically</b>.
  </p>
  <p>
    One thing people should <i>learn</i> from our visualization is that statistics does <b>not</b> 
    have to be super complicated. <i>Anyone</i> can learn how to card count, or even apply 
    statistics in their daily life whether that be with fitness tracking, 
    consumerism/price tracking, social media trends, or even personal finance. Fundementally,
    our visualization aims to <b>demystify</b> the confusion around math and statistics 
    as card counting at the fundamental level is simply keeping tracking of the 
    ratio of low items to high items. 
  </p>
  <p>
    We aimed for our visualization to <b>succeed</b> at explaining this by going through the process of 
    betting and card counting <i>step by step</i>. We only add one new piece of 
    information at a time and use simulations and visuals to <b>aid</b> the text 
    explanation. Our hope is that a <b>combination</b> of these elements helps users 
    <i>fundamentally</i> understand the mechanics behind not only betting and card 
    counting in this blackjack application, but math and statistics at the higher 
    level. <b>Thank You</b> for comings this far and to conclude we'll leave you with another opportunity to <i>test</i> yourself.
  </p>
  <p>
    <b>Try playing another game to see how your skills have improved!</b>
  </p>
</body>
<Base />
<body>
  <p style=text-align:center> <a href="https://github.com/lukelin15/CardCounting/tree/main">Repository</a></p>
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
    margin-bottom: 60px;
    margin-top: 60px;
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
    width: 100%;
    /* background-image: url('https://st.depositphotos.com/3215383/5028/i/450/depositphotos_50286385-stock-photo-nice-abstract-background.jpg'); */
    background-repeat: no-repeat;
    background-attachment: fixed;
    background-size: cover;
    color: #333;
    display: flex;
    flex-direction: column;
    justify-content: center;
    background-color: #f4efe9ff;
  }

  p {
    font-size: 1.2em;
    line-height: 1.5;
    padding: 20px;
    margin: 0 auto;
    width: 40%;
    /* text-indent: 30px; */
    font-family: "Roboto", serif;
  }

  a{
    font-family: "Roboto", serif;
  }

  table {
    width: 40%;
    font-size: 14px;
    border-collapse: collapse;
    font-family: 'Roboto', serif;
    border: 1px solid black;
    margin: 0 auto; 
    margin-top: 20px;
    margin-bottom: 20px;
    padding: 20px;
  }
  th, td {
  padding: 10px;
  font-family: "Roboto", serif;
  width: 50%; 
  background-color: #ffffff;
  }

  th {
    background-color: #ece4d9;
  }

</style>

