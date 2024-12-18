<script>
import Card from "../grid/Card.svelte";
import Grid from "../grid/Grid.svelte";
import Row from "../grid/Row.svelte";

  let {player1 = $bindable([]), 
        player2 = $bindable([]),
        table = $bindable([]),
        restartGame, 
        doTheCurrentPlayerNeedClose = $bindable(false)} = $props();//$bindable is a function that binds the player1, player2, and table to the deck from the page. Props are the properties of the component.
  let theEnd = $derived((player1.length===0 || player2.length===0) && !doTheCurrentPlayerNeedClose && table.length)
  let currentPlayer1Points = $derived(calculateCards(player1, table[table.length-1]))//take last card from table
  let currentPlayer2Points = $derived(calculateCards(player2, table[table.length-1]))
  function calculateCards(cards, lastCard){
    const six = cards.filter(card=>{
        return card.value === "6"
    }).length*6
    const seven = cards.filter(card=>{
        return card.value === "7"
    }).length*7
    const eight = cards.filter(card=>{
        return card.value === "8"
    }).length*8
    const ten = cards.filter(card=>{
        return card.value === "10"
    }).length*10
    const jack = cards.filter(card=>{
        return card.value === "J"
    }).length*2
    const queen = cards.filter(card=>{
        return card.value === "Q"
    }).length*3
    const king = cards.filter(card=>{
        return card.value === "K"
    }).length*4
    const ace = cards.filter(card=>{
        return card.value === "A"
    }).length*11
    let points = six+seven+eight+ten+jack+queen+king+ace
    if(cards.length===1){
        const card = cards[0]//save card in new variable to continue use it, take card from array
        if(card.value === "Q" && card.suit === "spades"){
            points = 40
        }else if(card.value === "Q"){//if still Queen but not spades
            points = 20
        }
    }
    if(cards.length === 0){
        if(lastCard?.value === "Q" && lastCard?.suit === "spades"){
            points = -40
        }else if(lastCard?.value === "Q"){//if still Queen but not spades
            points = -20
        }
    }
   return points
  }

  $effect(() => {
        console.log(currentPlayer1Points, currentPlayer2Points);
    })
    let results = $state([])
    let totalPointsPlayer1 = $derived(currentPlayer1Points+results.reduce((acc, result)=>{
      return acc + result.player1
    },0))//acc is accumulator who everytime for each item accum.information
    let totalPointsPlayer2 = $derived(currentPlayer2Points+results.reduce((acc, result)=>{
      return acc + result.player2
    },0))

</script>



{#if !theEnd}
    <slot>
        </slot>
{:else}
<Row>
    <Grid>
        <Card col={4}>
            <h2>Player1</h2>
            {currentPlayer1Points}
        </Card>
        <Card col={4}>
            <h2>Player2</h2>
            {currentPlayer2Points}
        </Card>
        <Card col={4}>
            {#if totalPointsPlayer1<=101 && totalPointsPlayer2<=101}
                <button onclick={() =>{
                    results = [...results, {player1:currentPlayer1Points, player2:currentPlayer2Points}]
                    if(totalPointsPlayer1 == 101){
                      results = [...results, {player1:-101, player2:0}]//0 means +0 here
                    }
                    if(totalPointsPlayer2 == 101){
                        results = [...results, {player1:0, player2:-101}]
                    }
                    if(totalPointsPlayer1 == 100){
                      results = [...results, {player1:-50, player2:0}]
                    }
                    if(totalPointsPlayer2 == 100){
                        results = [...results, {player1:0, player2:-50}]
                    }
                    restartGame()
                }}>Next round</button>
            {:else if totalPointsPlayer1<totalPointsPlayer2}
                Win Player1
            {:else}
                Win Player2
            {/if}
        </Card>
        <Card col={12}>
            <table>
               <thead>
                    <tr>
                        <th>Player1</th>
                        <th>Player2</th>
                    </tr>
               </thead>
                <tbody>
                    {#each results as result}
                        <tr>
                            <td>{result.player1}</td>
                            <td>{result.player2}</td>
                        </tr>
                    {/each}
                    <tr>
                        <td>{currentPlayer1Points}</td>
                        <td>{currentPlayer2Points}</td>  
                    </tr>
                    <tr class="result">
                        <td>{totalPointsPlayer1}</td>
                        <td>{totalPointsPlayer2}</td>
                    </tr>
                </tbody>
            </table>
        </Card>
    </Grid>
</Row>
{/if}
<style>
    .result{
        color:blue
    }
</style>