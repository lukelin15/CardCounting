<script>
    // Create a deck of cards
    let deck = [
        'A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K',
        'A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K',
        'A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K',
        'A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'
    ];
    let suits = ['♠', '♣', '♥', '♦'];
    deck = deck.map(card => {
        return card + suits[Math.floor(Math.random() * suits.length)];
    });

    // The current card
    let card = '';


    // Shuffle the deck
    function shuffle() {
        for (let i = deck.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [deck[i], deck[j]] = [deck[j], deck[i]];
        }
    }

    // Deal a card
    function deal() {
        return deck.pop();
    }

    // Click for a new card
    function newCard() {
        shuffle();
        card = deal();
    }
</script>

<style>
    .card {
        font-size: 3em;
        text-align: center;
        padding: 1em;
        border: 1px solid #ccc;
        border-radius: 5px;
        margin: 1em;
    }
</style>

<body>
    <button class="card" on:click={newCard} role="button">
        {card}
    </button>
</body>
