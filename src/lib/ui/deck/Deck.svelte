<script>
    import { Singlecard } from "$lib/ui/Singlecard";//Don't forget to import the Card component
const deck = [// if I put const {deck}, it doesn't work, because it's not an object. I can use just like let {a, b} = {a:1, b:2}/let [c, d] = [3,4]
    {suit: "hearts", value: "A"},
    {suit: "hearts", value: "6"},
    {suit: "hearts", value: "7"},
    {suit: "hearts", value: "8"},
    {suit: "hearts", value: "9"},
    {suit: "hearts", value: "10"},
    {suit: "hearts", value: "J"},
    {suit: "hearts", value: "Q"},
    {suit: "hearts", value: "K"},
    {suit: "spades", value: "A"},
    {suit: "spades", value: "6"},
    {suit: "spades", value: "7"},
    {suit: "spades", value: "8"},
    {suit: "spades", value: "9"},
    {suit: "spades", value: "10"},
    {suit: "spades", value: "J"},
    {suit: "spades", value: "Q"},
    {suit: "spades", value: "K"},
    {suit: "clubs", value: "A"},
    {suit: "clubs", value: "6"},
    {suit: "clubs", value: "7"},
    {suit: "clubs", value: "8"},
    {suit: "clubs", value: "9"},
    {suit: "clubs", value: "10"},
    {suit: "clubs", value: "J"},
    {suit: "clubs", value: "Q"},
    {suit: "clubs", value: "K"},
    {suit: "diamonds", value: "A"},
    {suit: "diamonds", value: "6"},
    {suit: "diamonds", value: "7"},
    {suit: "diamonds", value: "8"},
    {suit: "diamonds", value: "9"},
    {suit: "diamonds", value: "10"},
    {suit: "diamonds", value: "J"},
    {suit: "diamonds", value: "Q"},
    {suit: "diamonds", value: "K"},
]
let shuffledDeck = $state(shuffle(deck));//shuffle is a function that shuffles the deck

function shuffle(deck) {
    return deck.sort(() => Math.random() - 0.5);
}
export function deal(ITookCard) {
    if(ITookCard){
        didTheCurrentPlayerGetCard = true;
    }
    const thatCard = shuffledDeck.pop();
    reshuffleDeck()
    return thatCard;
}
let {deckClicked, player1 = $bindable([]), player2 = $bindable([]), table = $bindable([]), whoTurn = $bindable(2), didTheCurrentPlayerGetCard = $bindable(false), checkIfTheCurrentPlayerNeedClose, shouldTakeCardForever= $bindable(false)} = $props();//$bindable is a function that binds the player1, player2, and table to the deck from the page. Props are the properties of the component.
function reshuffleDeck() {
    if(shuffledDeck.length === 0){
        shuffledDeck = shuffle(table.filter((card, i)=>{//[3,4,7] i=0 => i<2
            return i<table.length-1//return true
        }))
        table = table.filter((card, i)=>{
            return i===table.length-1
        })
    }  
}
</script>

<div class="deck" onclick={() => {
    if(shuffledDeck.length===36){
     player1 = [deal(),deal(),deal(),deal()]
     player2 = [deal(),deal(),deal()]
     const firstCard = deal(true);
     table = [firstCard]
     checkIfTheCurrentPlayerNeedClose(firstCard);
    }else{
        if(!didTheCurrentPlayerGetCard || shouldTakeCardForever){
            if(whoTurn === 1){
                player1=[...player1, deal(true)]//IToolCard = true
            }else{
                player2=[...player2, deal(true)]
            }
        }
       
    }
    deckClicked()
}}>
    {#each shuffledDeck as card, i}
        <div 
            class="card-wrapper"
            style="--index: {i}">
            <Singlecard suit={card.suit} value={card.value} />
        </div>
    {/each}
</div>


<style>
    .deck {
        position: relative;
        width: 100px;  /* adjust based on your card width */
        height: 140px; /* adjust based on your card height */
    }

    .card-wrapper {
        position: absolute;
        transform: translate(calc(var(--index) * 0.3px), calc(var(--index) * 0.3px));
        transition: transform 0.2s ease;
    }

    .card-wrapper:hover {
        transform: translate(calc(var(--index) * 0.3px), calc(var(--index) * 0.3px - 10px));
    }
</style>
