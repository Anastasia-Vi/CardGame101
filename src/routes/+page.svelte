<script>
    import { Card, Grid, Row } from "$lib/ui/Grid";
    import { Deck } from "$lib/ui/Deck";
    import { Singlecard } from "$lib/ui/Singlecard";
    import {Picksuit} from "$lib/ui/Picksuit";
    import {Game} from"$lib/ui/Game";
    let deck;
    let card;//card does not have a value yet empty
    let player1 = $state([]);
    let player2 = $state([]);
    let table = $state([]);
    
    function deckClicked(){}//empty function
    function canPutOnTable(card,player){
        if(player !== whoTurn){//if the player is not the one who's turn it is, return false
            return false;//I can't put card on table (look at if(canPutOnTable(card,1)) player1
        }
        if(card.value === "Q"){
            putQueenOnTable = true
            return true; //I can put this card on table on any card
        }
        if(specialSuit){//if specialSuit true
            const canReturn = card.value === table[table.length - 1].value || card.suit === specialSuit; //
            if(canReturn){//If I put special card (suit, what's another player ordered special suit)
                specialSuit = false; //after I can put only the same value or suit as usual
            }
            return canReturn
        }

        return card.value === table[table.length - 1].value || card.suit === table[table.length - 1].suit;//it checks if the card value is the same as the last card on the table or if the card suit is the same as the last card on the table
    }
    let whoTurn = $state(2);
    let doTheCurrentPlayerNeedClose = $state(false);
    let didTheCurrentPlayerGetCard = $state(false);
    let shouldTakeCardForever = $state(false);
    let specialSuit = $state(false);
    $effect(() => {//effect just listening situation
        console.log(didTheCurrentPlayerGetCard);
    })
    function checkIfTheCurrentPlayerNeedClose(card){
        if(card.value === "6" || card.value === "7" || (card.value === "K" && card.suit === "spades") || card.value === "A"){//(K in () because it's both for true value and suit)
            doTheCurrentPlayerNeedClose = true;
            didTheCurrentPlayerGetCard = false;
            if(card.value === "6"){
               shouldTakeCardForever = true;
            }else{
                shouldTakeCardForever = false;
            }
            if(card.value === "7"){
                if(whoTurn ===1){
                    player2 = [...player2, deck.deal(), deck.deal()]
                }else{
                    player1 = [...player1, deck.deal(), deck.deal()]
                }
                if(card.suit === "spades"){
                    if(whoTurn ===1){
                        player2 = [...player2, deck.deal(), deck.deal()]
                    }else{
                        player1 = [...player1, deck.deal(), deck.deal()]
                    }
                }
            }
            if(card.value === "K" && card.suit === "spades"){
                if(whoTurn ===1){
                    player2 = [...player2, deck.deal(), deck.deal(), deck.deal(),deck.deal(),deck.deal()]
                }else{
                    player1 = [...player1, deck.deal(), deck.deal(), deck.deal(),deck.deal(),deck.deal()]
                }
            }
        }else{
            doTheCurrentPlayerNeedClose = false;
            shouldTakeCardForever = false;
        }
    }
    function selectedSuit(suit){
        whoTurn = (whoTurn ===2) ? 1 : 2
        didTheCurrentPlayerGetCard = false
        specialSuit = suit
        putQueenOnTable = false
    }
    function restartGame(){
        player1 = []
        player2 = []
        table = []
        whoTurn = 2
        doTheCurrentPlayerNeedClose = false
        didTheCurrentPlayerGetCard = false
        shouldTakeCardForever = false
        specialSuit = false
    }
    let putQueenOnTable = $state(false);
  </script>
    <Game
        bind:player1={player1} 
        bind:player2={player2}  
        bind:table={table} 
        bind:doTheCurrentPlayerNeedClose={doTheCurrentPlayerNeedClose}
        {restartGame}>
        <Row>
        <Grid>
                <Card col={12}>
                    <div class="player-area player1">
                        <div class="cards-container">
                            {#each player1 as card, i}
                            <div class="card-wrapper" onclick={() => {
                                if(canPutOnTable(card,1)){//if fuction true, do this, 1 is the player 1 like second parameter
                                    table = [...table, card];
                                    checkIfTheCurrentPlayerNeedClose(card);
                                    player1 = player1.filter(c => c !== card);//current card clicked is removed from player1, this condition been evaulated to true for all cards in player1
                                    if(!doTheCurrentPlayerNeedClose){
                                        if(card.value !== "Q"){
                                            whoTurn = 2;
                                            didTheCurrentPlayerGetCard = false;
                                        }
                                    }
                                }   
                            }}
                                style="--index: {i}">
                                <Singlecard suit={card?.suit} value={card?.value} hide={false} />
                            </div>
                            {/each}
                        </div>
                    </div>
                </Card>
                {#if putQueenOnTable && whoTurn === 1}<!--if last card is Q show selectedSuit. ?.-read the key if value exists, if Q exists read it-->
                    <Card>
                        <Picksuit
                            {selectedSuit}>
                        </Picksuit>
                    </Card>   
                {/if}
                <Card col={3} mcol={3}>
                    <Deck
                            bind:this={deck}
                            bind:player1={player1} 
                            bind:player2={player2} 
                            bind:table={table} 
                            {deckClicked} 
                            bind:whoTurn={whoTurn} 
                            bind:didTheCurrentPlayerGetCard={didTheCurrentPlayerGetCard} 
                            {checkIfTheCurrentPlayerNeedClose}
                            bind:shouldTakeCardForever/>
                </Card>
                <Card col={6} mcol={6}>
                    <div class="table-area">
                        <div class="cards-container">
                            {#each table as card, i}
                                <div class="card-wrapper"
                                    style="--index: {i}">
                                    <Singlecard suit={card?.suit} value={card?.value} hide={false} />
                                </div>
                            {/each}
                        </div>
                    </div>
                </Card>
                <Card col={3} mcol={3}>
                    {#if player1.length > 0 && !shouldTakeCardForever && whoTurn === 1 && didTheCurrentPlayerGetCard}
                        <button onclick={() => {
                            whoTurn = 2
                            didTheCurrentPlayerGetCard = false;
                        }}>
                            Pass
                        </button>
                    {:else if player2.length > 0 && !shouldTakeCardForever && whoTurn === 2 && didTheCurrentPlayerGetCard}
                        <button onclick={() => {
                            whoTurn = 1;
                            didTheCurrentPlayerGetCard = false;
                        }
                    }>
                            Pass
                        </button>
                    {:else}
                        <p>Turn {whoTurn}</p>
                    {/if}
                </Card>
                {#if putQueenOnTable && whoTurn === 2}<!--if last card is Q show selectedSuit. ?.-read the key if value exists, if Q exists read it-->
                    <Card>
                        <Picksuit
                            {selectedSuit}>
                        </Picksuit>
                    </Card>   
                {/if}
                <Card col={12}>
                    <div class="player-area player2">
                        <div class="cards-container">
                            {#each player2 as card, i}
                                <div class="card-wrapper" onclick={() => {
                                    if(canPutOnTable(card,2)){
                                        table = [...table, card];
                                        checkIfTheCurrentPlayerNeedClose(card);
                                        player2 = player2.filter(c => c !== card);
                                        if(!doTheCurrentPlayerNeedClose){
                                            if(card.value !== "Q"){
                                                whoTurn = 1;
                                                didTheCurrentPlayerGetCard = false;
                                            }
                                        }
                                    }
                                }}
                                    style="--index: {i}">
                                    <Singlecard suit={card?.suit} value={card?.value} hide={false} />
                                </div>
                            {/each}
                        </div>
                    </div>
                </Card>
            </Grid>
        </Row>
    </Game>
    
    <style>

        .player-area, .table-area {
            padding: 1rem;
            border-radius: 6px;
            background: white;
            
        }
    
        .cards-container {
            min-height: 140px;
            padding: 0.5rem;
            position: relative;
            margin:1rem auto;
        }

        .table-area .cards-container .card-wrapper {
            position: absolute;
            transform: translate(calc(var(--index) * 0.3px), calc(var(--index) * 0.3px));
            transition: transform 0.2s ease;
        }

        .table-area .cards-container .card-wrapper:hover {
            transform: translate(calc(var(--index) * 0.3px), calc(var(--index) * 0.3px - 10px));
        }
        .player-area .cards-container .card-wrapper {
            position: absolute;
            transform: translate(calc(var(--index) * 20px), calc(var(--index) * 0px));
            transition: transform 0.2s ease;
        }

        .player-area .cards-container:hover .card-wrapper{
            transform: translate(calc(var(--index) * 30px), calc(var(--index) * 0px));
        }
        @media (max-width: 767px) {
            .game-container {
                grid-template-rows: auto auto auto;
                gap: 1rem;
                padding: 1.5rem;
            }
            .player-area, .table-area {
            padding: 0rem;  
            background: none;
        }
            .cards-container{
                min-height: 100px;
                padding: 0rem;
                position: relative;
                margin:0 auto;
            }
            .table-area .cards-container{
                padding: 0 2rem;
            }
        }
    </style>