<script>
    export let number = 0;
    
    let firstNumber = number;
    $: if (number !== firstNumber) firstInput = false;

    export let min = 0;
    export let max = 0;
    export let currency = '';
    export let tip = '';
    export let changeClass = null;
    export let active = false;
    export let firstInput = true;
    let allowedButtons = [
        'Backspace', 'Delete', 'ArrowLeft', 'ArrowRight'
    ]

    let displayNumber = Math.round(number).toLocaleString('ru');
    $: {
        displayNumber = Math.round(number).toLocaleString('ru');
        currentBindInput = displayNumber;
    }

    // only numbers
    function checkInput(e) {

        // if ( e.key == 'KeyA' && e.ctrlKey == true )
        if ( !e.key.match(/[0-9]/) && allowedButtons.indexOf(e.key) == -1 ) {
            e.preventDefault()
            return
        }   
        
        getTextWidth()
    }

    function getTextWidth(value) { 
     
        let inputText = value; 
        let font = "16px 'Aeroport'"; 
    
        let canvas = document.createElement("canvas"); 
        let context = canvas.getContext("2d"); 
        context.font = font; 
        let width = context.measureText(inputText).width; 
        let formattedWidth = Math.ceil(width) + "px"; 
    
        return formattedWidth;
    }
    
    let currentBindInput = displayNumber;

    // при focusout выставляем отрицательный инпут, и проверяем правильность значения. Если да, то присваиваем экспортное значение ему и делаем формат
    function didInput() {
        let inputNumber = Number( currentBindInput.replace(/\D/g, "") );
        if (inputNumber > max) inputNumber = max;
        if (inputNumber < min) inputNumber = min;

        // Присваиваем экспортному number введённое значение
        number = inputNumber;

        // А в поле оставляем форматированное значение
        displayNumber = Math.round(number).toLocaleString('ru');
        currentBindInput = displayNumber
    }

    // при focusin инпут биндится на displayNumber
    function prepareInput() {
        let inputNumber = number;
        currentBindInput = inputNumber.toString()
    }

    let input;
</script>

<!-- svelte-ignore a11y-aria-attributes -->
<div class={changeClass ?? 'default'} on:click={() => {input.focus()}}>
    {#if active}
        <anotherHolder>
            <input 
                style={` width: ${ getTextWidth(currentBindInput) }; color: ${firstInput ? 'grey' : 'black'} `}
                type="text"
                on:keydown={(e) => checkInput(e)}
                on:focusin={prepareInput}
                on:focusout={didInput} 
                bind:value={currentBindInput}
                bind:this={input} 
                class='h2-input'
            />
            <span style={` color: ${firstInput ? 'grey' : 'black'} `}>
                {currency}
            </span>
        </anotherHolder>
    {:else}
        <h2 style={` color: ${firstInput ? 'grey' : 'black'} `}>
            { displayNumber + ' ' + currency }
        </h2>
    {/if}
    
    {#if tip}
        <h4>
            {tip}
        </h4>
    {/if}
</div>


<style>

    anotherHolder {
        display: flex;
        align-content: baseline;
        align-items: baseline;
        gap: 5px;
        align-self: flex-end;
    }

    .default {
        padding: 15px;
        display: flex;
    }

    .default h2 {
        font-size: 16px;
        color: grey !important;
    }

    .h2-input {
        font-size: 16px;
        font-family: 'Aeroport';
        background-color: transparent;
        border: 0;
        display: flex;
        align-self: baseline;
        width: min-content;
        transition: 0.3s;
    }

    .h2-input:active {
        border: 0;
    }

    :-moz-focusring {
        outline: none; 
    }

    @media screen 
        and (max-width: 480px){
        div {
            min-width: 107px !important;
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
        transition: 0.3s;
    }
</style>