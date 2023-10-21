<script lang="ts">
    import '../assets/bankForm.scss'
    // import AdvancedPhoneInput from './AdvancedPhoneInput.svelte';
    // import type {
	// 	E164Number
	// } from 'svelte-tel-input/types';
    
    let name = '';
    let telephone = '';
    let dateOfBirth = '';

    let acceptPolicy = false;
    
    let dateInputType = 'text'

    function sendData() {
        let elements = document.querySelector(window.bankForm.donorFormID + " form").elements;

        elements.Name.value = name;
        elements.Tel.value = telephone;
        elements.Date.value = (new Date(dateOfBirth)).toLocaleDateString();

        for (const key in elements) {
            if (elements[key].type === 'submit') {
                elements[key].click()
                return
            }
        }

    }

    let canSend = false;
    function checkThis(i) {
        canSend = true;
        console.log(i)
        if ( i.type !== 'checkbox' )
            if ( !i.checkValidity() ) {
                i.reportValidity();
            }

        document.querySelectorAll('.inputHolder input').forEach(i => {
            console.log(i, i.checkValidity())
            if ( !i.checkValidity() ) canSend = false
        })

        if (!acceptPolicy)
            canSend = false

        console.log(acceptPolicy, canSend)
    }

</script>

<outputForm>
    <h3>
        Отправьте заявку, мы свяжемся с вами и обсудим условия ипотеки
    </h3>
    <div class="inputHolder">
        <input bind:value={name} on:blur={(e) => checkThis(e.target)} required type="text" pattern="[A-z]+|[А-я]+" placeholder="Фамилия, имя, отчество"/>
        <input bind:value={telephone} on:blur={(e) => checkThis(e.target)} required type="text" pattern="\+[0-9]+" placeholder="+7 (927) 226-12-34"/>
        <!-- <AdvancedPhoneInput 
            {selectedCountry}
            bind:value={phoneNumber}
        /> -->
        {#if dateInputType == 'date'}
            <input bind:value={dateOfBirth} on:change={(e) => checkThis(e.target)} required type="date" />
        {:else}
            <input required type="text" on:focus={() => dateInputType='date'} placeholder="Дата рождения"/>
        {/if}
        
    </div>

    <customCheckbox>
        <label class="container-checkbox">
            Я соглашаюсь с <a href='#'>политикой конфиденциальности</a>
            <input on:click={() => acceptPolicy = !acceptPolicy} on:change={(e) => checkThis(e.target)} type="checkbox" checked={acceptPolicy} >
            <span class="checkmark"></span>
        </label>
    </customCheckbox>
    <buttonContainer on:click={sendData} on:keydown={() => {}}>
        <button disabled={!canSend}>
            Отправить данные
            <blackThing style="width: 5px; right: -10px" />
            <blackThing style="width: 6px; right: -18px;" />
        </button>
        
    </buttonContainer>
    
</outputForm>

<style>
    buttonContainer {
        display: flex;
        flex-direction: row;
        gap: 3px;
    }

    blackThing {
        height: auto;
        background-color: inherit;
        width: 5px;
        display: block;
        opacity: 1;
        position: absolute;
        top: -1px;
        height: calc(100% + 2px);
        transition: 0.3s;
    }

    buttonContainer:hover blackThing {
        opacity: 1;
        right: 0 !important;
        transition: 0.3s;
    }

    button:disabled{
        cursor: default;
        opacity: 0.7;
    }
    button {
        background-color: #2C3442;
        color: white;
        width: 189px;
        height: 49px;
        display: flex;
        align-content: center;
        border-radius: 0;
        align-items: center;
        font-size: 12px;
        justify-content: center;
        position: relative;
    }
    customCheckbox {
        padding-top: 15px;
        padding-bottom: 5px;
    }

    label {
        color: black;
        font-size: 12px;
        text-align: left;
    }

    a {
        color: #404040 !important; 
    }

    .inputHolder {
        padding-top: 20px;
        display: flex;
        flex-direction: column;
        gap: 10px;
        width: 100%;
        
    }

    input {
        background-color: white;
        border: 0;
        color: black;
        padding: 1.5em 2em 1.5em 2em;
        box-sizing: border-box;
    }

    outputForm {
        display: flex;
        flex-direction: column;
        align-items: baseline;
    }


    /* кастомный чекбокс */

    /* The container */
    .container-checkbox {
        display: block;
        position: relative;
        padding-left: 30px;
        padding-top: 1px;
        margin-bottom: 12px;
        cursor: pointer;
        font-size: 12px;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
    }

    /* Hide the browser's default checkbox */
    .container-checkbox input {
        position: absolute;
        opacity: 0;
        cursor: pointer;
    }

    /* Create a custom checkbox */
    .container-checkbox .checkmark {
        position: absolute;
        top: 0;
        left: 0;
        height: 18px;
        width: 18px;
        background-color: #FFF5D6;
        border: 2px solid #2C3442;
    }

    /* On mouse-over, add a grey background color */
    .container-checkbox:hover input ~ .checkmark {
        background-color: #FFF5D6;
    }

    /* When the checkbox is checked, add a blue background */
    .container-checkbox input:checked ~ .checkmark {
        background-color: #FFF5D6;
    }

    @media screen and (max-width: 480px) {
        .container-checkbox {
            padding-top: 0px;
            line-height: 0.9;
        }

        .container-checkbox .checkmark {
            background-color: rgb(255, 222, 138);
        }

        .container-checkbox:hover input ~ .checkmark {
        background-color: rgb(255, 222, 138);
        }

        /* When the checkbox is checked, add a blue background */
        .container-checkbox input:checked ~ .checkmark {
            background-color: rgb(255, 222, 138);
        }
    }

    /* Create the checkmark/indicator (hidden when not checked) */
    .container-checkbox .checkmark:after {
        content: "";
        position: absolute;
        display: none;
    }

    /* Show the checkmark when checked */
    .container-checkbox input:checked ~ .checkmark:after {
        display: block;
    }

    /* Style the checkmark/indicator */
    .container-checkbox .checkmark:after {
        left: 5.5px;
        top: 1px;
        width: 5px;
        height: 9px;
        border: solid black;
        border-width: 0 3px 3px 0;
        -webkit-transform: rotate(45deg);
        -ms-transform: rotate(45deg);
        transform: rotate(45deg);
    }

</style>