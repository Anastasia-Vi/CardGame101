<script>
    const suitSymbols = {
        hearts: '♥',
        diamonds: '♦',
        clubs: '♣',
        spades: '♠'
    };
    let {selectedSuit} = $props();
</script>

{#each Object.keys(suitSymbols) as suit}<!--I want an array with symbols inside-->
    <div class="card {`suit-${suit}`}" onclick={()=>{
          selectedSuit(suit)
        }
        }>
        <div class="card-center suit">{suitSymbols[suit]}</div>
    </div>
{/each}
<style>
    .card {
        width: 15px;
        height: 30px;
        background: white;
        border-radius: 8px;
        border: 1px solid #ccc;
        position: relative;
        display: inline-flex;
        margin: 5px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        transition: transform 0.2s ease;
    }

    .card:hover {
        transform: translateY(-5px);
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    }

    .card-corner {
        position: absolute;
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 5px;
    }

    .top-left {
        top: 0;
        left: 0;
    }

    .bottom-right {
        bottom: 0;
        right: 0;
        transform: rotate(180deg);
    }

    .value {
        font-size: 1.2rem;
        font-weight: bold;
        color: var(--card-color);
    }

    .suit {
        font-size: 1.2rem;
        color: var(--card-color);
    }

    .card-center {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        font-size: 2.5rem;
    }

    /* Replace the problematic selectors with these */
    :global(.suit-hearts), :global(.suit-diamonds) {
        --card-color: #e44545;
    }

    :global(.suit-clubs), :global(.suit-spades) {
        --card-color: #2c3e50;
    }

    .hidden {
        background: #2c3e50;
        background-image: repeating-linear-gradient(
            45deg,
            transparent,
            transparent 10px,
            #34495e 10px,
            #34495e 20px
        );
    }

    .card-back {
        width: 100%;
        height: 100%;
        border-radius: 8px;
    }

    @media(max-width: 767px){
        .card {
        zoom:0.6
        }

    }
</style>