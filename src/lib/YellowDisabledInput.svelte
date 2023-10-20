<script>
    export let number = "";
    export let currency = '';
    export let tip = '';
    export let changeClass = null;
    $: number = Math.round(number).toLocaleString('ru')
</script>


<div class={changeClass ?? 'default'}>
    <h2>
        { number + ' ' + currency }
    </h2>
    {#if tip}
        <h4>
            {tip}
        </h4>
    {/if}
</div>


<style>

    .default {
        padding: 15px;
    }

    .default h2 {
        font-size: 16px;
    }

    @media screen 
        and (max-width: 480px){
        div {
            min-width: 90px !important;
        }

        h4 {
            white-space: break-spaces !important;
        }
    }

    @media screen 
        and (min-width: 480px) 
        and (max-width: 640px){
        div {
            min-width: 163px !important;
        }
    }

    div {
        background-color: #FFF5D6;
        width: auto;
        min-width: 198px;
    }

    h4 {
        color: #A6A9AF;
        white-space: nowrap;
    }
    
    h2 {
        white-space: nowrap;
    }
</style>