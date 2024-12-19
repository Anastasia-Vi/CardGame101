<script>
    export let suit;
    export let value;
    export let hide = true;
    const suitSymbols = {
        hearts: '♥',
        diamonds: '♦',
        clubs: '♣',
        spades: '♠'
    };

    $: isRed = suit === 'hearts' || suit === 'diamonds';
    $: suitClass = `suit-${suit}`;//if {suit} change=>suitClass change too. suit-clubs
</script>

<div class="card {suitClass} {hide ? 'hidden' : ''}"><!--for css-->
    {#if !hide}
        <div class="card-corner top-left">
            <div class="value">{value}</div>
            <div class="suit">{suitSymbols[suit]}</div>
        </div>
        <div class="card-center suit">{suitSymbols[suit]}</div>
        <div class="card-corner bottom-right">
            <div class="value">{value}</div>
            <div class="suit">{suitSymbols[suit]}</div>
        </div>
    {:else}
        <div class="card-back"></div>
    {/if}
</div>

<style>
    .card {
        width: 75px;
        height: 110px;
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
    @media (max-width: 767px){/* I put here what I really need to change, DON"T COPY */
        .card {
            /*width: 45px;  
            height: 60px; I can use this but I need fix suit and value or/=>*/
            zoom: 0.7;
        }
        
        
    }
</style>