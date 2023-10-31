<script>
  import '../assets/bankForm.scss'
  import Slider from './Slider.svelte'
  import YellowDisabledInput from './YellowDisabledInput.svelte'
  import OutputForm from './OutputForm.svelte'

  // Стоимость недвижимости
  let costOfRealEstate = window.bankForm.costOfRealEstate

  // Первоначальный взнос
  let initialFee = window.bankForm.initialFee

  // Срок
  let mortgageYears = window.bankForm.mortgageYears;

  // Ставка
  let bankRate = window.bankForm.bankRate * 100;

  // Правильное написание срока (год, года, лет)
  let correctYearPronounce = '';
  $: if (mortgageYears) {
    let myStr = mortgageYears.toString()
    
    if ( Number(myStr[myStr.length - 1]) == 1 && (mortgageYears < 10 || mortgageYears > 19) ) correctYearPronounce = 'год'
    else if ( Number(myStr[myStr.length - 1]) <= 5 && Number(myStr[myStr.length - 1]) !== 0 && (mortgageYears < 10 || mortgageYears > 19) ) correctYearPronounce = 'года'
    else correctYearPronounce = 'лет' 
  } 

  // Сначала все инпуты серые. После изменения - становятся черными.
  let firstInput = true;
  $: if (
    costOfRealEstate !== window.bankForm.costOfRealEstate ||
    initialFee !== window.bankForm.initialFee ||
    mortgageYears !== window.bankForm.mortgageYears
  ) firstInput = false;

  // 
  // Используем формулы, которые мы указали в другом блоке
  //

  // Первоначальный взнос в AED
  let calcInitialFee; 
  $: calcInitialFee = window.bankForm.f.getCalcInitialFee(costOfRealEstate, initialFee)

  // Сумма кредита
  let loanAmount = 31740000;
  $: costOfRealEstate, loanAmount = window.bankForm.f.getLoanAmount(costOfRealEstate, initialFee, mortgageYears)

  // Ежемесячный платёж
  let monthlyFee = 0;
  $: monthlyFee = window.bankForm.f.getMonthlyFee(loanAmount, mortgageYears)
  
  let screenWidth;
</script>

<!-- Для рендеринга на основе размера окна -->
<svelte:window bind:innerWidth={screenWidth} />

<bankForm>
    <fContainer>
        <fRow>
            <fCol>
                <calcEntity>
                    <yellowInputHolder>
                        <YellowDisabledInput active={true} min={500000} max={60000000} bind:number={costOfRealEstate} currency="AED" />
                    </yellowInputHolder>
                    <h4>Стоимость недвижимости</h4>
                    <Slider bind:value={costOfRealEstate} min={500000} max={60000000} step={10000} minName='500 000 AED' maxName='60 000 000 AED' />
                </calcEntity>
                <calcEntity>
                    <yellowInputHolder>
                        <YellowDisabledInput active={true} min={20} max={80} bind:number={initialFee} currency="%" />
                        <YellowDisabledInput number={calcInitialFee} currency="AED" />
                    </yellowInputHolder>
                    <h4>Первоначальный взнос</h4>
                    <Slider bind:value={initialFee} min={20} max={80} step={1} minName='20%' maxName='80%' />
                </calcEntity>
                <calcEntity>
                    <yellowInputHolder>
                        <YellowDisabledInput active={true} min={1} max={20} bind:number={mortgageYears} currency={correctYearPronounce}/>
                    </yellowInputHolder>
                    <h4>Срок</h4>
                    <Slider bind:value={mortgageYears} min={1} max={20} step={1} minName='1 год' maxName='20 лет' />
                </calcEntity>
            </fCol>
            <fCol>
                <div class='zindex2'>
                    {#if screenWidth > 480}
                    <yellowBackground>
                        <yellowInputHolder>
                            <YellowDisabledInput {firstInput} changeClass={'secondCol'} number={loanAmount} currency="AED" tip="Сумма кредита" />
                            <YellowDisabledInput {firstInput} changeClass={'secondCol'} number={monthlyFee} currency="AED" tip="Ежемесячный платёж" />
                        </yellowInputHolder>
                        <yellowInputHolder>
                            <YellowDisabledInput {firstInput} changeClass={'secondCol'} number={bankRate} currency="%" tip="Ставка" />
                        </yellowInputHolder>
                        
                        <div>
                            <OutputForm />
                        </div>
                    </yellowBackground>
                    {:else}
                    <!-- iPhone -->
                    <funnyBlocks>
                        <funnyColorBlock style="background-color: #fff5d6">
                            <YellowDisabledInput {firstInput} changeClass={'secondCol funnyInner'} number={loanAmount} currency="AED" tip="Сумма кредита" />
                        </funnyColorBlock>
                        <funnyColorBlock style="background-color: #ffe9b0">
                            <YellowDisabledInput {firstInput} changeClass={'secondCol funnyInner'} number={monthlyFee} currency="AED" tip="Ежемесячный платёж" />
                        </funnyColorBlock>
                        <funnyColorBlock style="background-color: #ffe9b0">
                            <YellowDisabledInput {firstInput} changeClass={'secondCol funnyInner'} number={bankRate} currency="%" tip="Ставка" />
                        </funnyColorBlock>
                        <funnyColorBlock style="background-color: #ffde8a">

                        </funnyColorBlock>
                    </funnyBlocks>
                    <yellowBackground>
                        <div>
                            <OutputForm />
                        </div>
                    </yellowBackground>
                    {/if}
                </div>
            </fCol>
        </fRow>
    </fContainer>
</bankForm>

<style>
    :root {
        --padding: 40px
    }
    
    :global(.funnyInner) {
        background-color: inherit !important;
    } 

    funnyBlocks {
        display: grid;
        grid-template-columns: 50% 50%;
        grid-row: auto auto;
    }

    funnyColorBlock {
        height: 145px;
        padding-left: var(--padding);
        padding-top: var(--padding);
    }
    
    
    :global(.secondCol) {
        padding: 15px;
        padding: 0px;
    }

    :global(.secondCol h2) {
        font-size: 26px;
    }

    @media screen 
    and (max-width: 480px){
        :root {
            --padding: 15px
        }

        bankForm {
            width: 320px !important;
            padding-bottom: 0 !important;
        }

        :global(.secondCol h2) {
            font-size: 18px;
        }

        fContainer {
            flex-direction: row;
        }

        fRow {
            flex-direction: column;
            gap: 25px;
            padding: 0;
        }

        fCol {
            position: relative;
            gap: 20px !important;
        }

        fCol:first-child {
            position: relative;
            box-sizing: border-box;
            padding:var(--padding);
            padding-bottom: 0;
        }

        :global(yellowinputholder .default:first-child) {
            width: 120px;
        }

        :global(yellowinputholder .default:nth-child(2)) {
            width: 43px;
        }

        yellowBackground {
            position: relative;
            height: 100%;
            padding: var(--padding) !important;
            background-color: #ffde8a !important;
        }
    }

    @media screen 
    and (min-width: 480px) 
    and (max-width: 640px){
        :root {
            --padding: 30px
        }

        bankForm {
            width: 460px !important;
            padding-bottom: 0 !important;
        }

        fContainer {
            flex-direction: row;
        }

        fRow {
            flex-direction: column;
            gap: var(--padding);
            padding: 0;
        }

        fCol {
            position: relative;
            gap: 40px !important;
        }

        yellowInputHolder {
            justify-content: space-between;
        }

        fCol:first-child {
            position: relative;
            box-sizing: border-box;
            padding:var(--padding);
            padding-bottom: 0;
        }

        yellowBackground {
            gap: 18px;
            padding: var(--padding) !important;
        }

        
    }

    @media screen 
    and (min-width: 640px) 
    and (max-width: 960px){
        :root {
            --padding: 40px
        }

        bankForm {
            width: 620px;
            padding-bottom: 0 !important;
        }

        fContainer {
            flex-direction: row;
        }

        fRow {
            flex-direction: column;
            gap: var(--padding);
            width: 100%;
            padding: 0;
        }

        :global(yellowbackground yellowInputHolder) {
            gap: 15px !important;
        }

        yellowInputHolder {
            gap: 10px !important;
        }

        fCol:first-child {
            position: relative;
            box-sizing: border-box;
            padding: var(--padding);
            padding-bottom: 0;
        }

        yellowBackground {
            gap: 20px;
            padding: var(--padding) !important;
        }
    }

    @media screen 
    and (min-width: 960px) 
    and (max-width: 1200px){
        :root {
            --padding: 40px
        }

        :global(.secondCol h2) {
            font-size: 22px;
        }

        bankForm {
            width: 940px;
            padding-bottom: 0 !important;
        }

        fCol:first-child {
            width: 390px;
        }

        :global(yellowbackground yellowInputHolder) {
            justify-content: space-between;
        }

        :global(yellowInputHolder div) {
            min-width: auto !important;
        }

        :global(calcEntity yellowinputholder div:first-child) {
            width: 198px !important;
            min-width: auto !important;
            box-sizing: border-box;
        }

        :global(calcEntity yellowinputholder div:nth-child(2)) {
            width: 180px !important;
            min-width: auto !important;
            box-sizing: border-box;
        }

        fRow {
            gap: var(--padding);
            width: 100%;
            padding: var(--padding);
        }


        yellowBackground {
            padding: 30px !important;
            gap: 20px;
        }
    }

    @media screen 
    and (min-width: 1201px){
        :root {
            --padding: 60px
        }
        
        fCol:first-child {
            width: 420px;
        }

        fCol:nth-child(2) {
            width: unset;
        }


        bankForm {
            width: 1160px;
        }

        fContainer {
            flex-direction: column;
        }

        /* :global(yellowbackground yellowInputHolder) {
            gap: 10px !important;
        } */

        :global(yellowbackground yellowInputHolder) {
            gap: 60px !important;
        }

        :global(yellowInputHolder div) {
            min-width: auto !important;
        }

        :global(calcEntity yellowinputholder div:first-child) {
            width: 198px !important;
            min-width: auto !important;
            box-sizing: border-box;
        }

        :global(calcEntity yellowinputholder div:nth-child(2)) {
            width: 180px !important;
            min-width: auto !important;
            box-sizing: border-box;
        }

        fRow {
            flex-direction: row;
            gap: 100px;
            padding: var(--padding);
        }

        fCol {
            
        }

        yellowBackground {
            gap: 20px;
            padding: 40px !important;
        }
    }

    fContainer {
        width: 100%;
        height: 100%;
        display: flex;
    }

    fRow {
        display: flex;
        height: 100%;
    } 

    fCol {
        width: 100%;
        display: flex;
        flex-direction: column;
        gap: var(--padding);
    }


    yellowBackground {
        background-color: #FFF5D6;
        display: flex;
        background-color: #FFF5D6;
        flex-direction: column;
        padding: 20px;
    }

    yellowInputHolder {
        display: flex;
        gap: 10px;
        overflow: hidden;
    }

    calcEntity h4 {
        padding-top: 10px;
    }
    
    .zindex2 {
        z-index: 2;
    }

    bankForm {
        height: auto;
        font-family: 'Aeroport';
        display: block;
        background-color: white;
    }

    calcEntity {
        display: flex;
        flex-direction: column;
        align-items: baseline;
    }



</style>