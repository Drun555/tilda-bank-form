<script lang="ts">
  import BankForm from './lib/BankForm.svelte'

  // Скрываем донор-форму
  let donorForm = document.querySelector( window.bankForm.donorFormID )
  donorForm.style.visibility = 'collapse'
  donorForm.style.position = 'absolute'
  donorForm.style.height = '0'
  
</script>

<main>
  <BankForm />
</main>

<style>
  div {
    display: flex;
    align-self: baseline;
    flex-direction: column;
    font-size: 54px;
    font-family: 'Aeroport';
    line-height: 1;
  }

  :global(body) {
    background: #f5f5f5
  }

  :global(#app) {
    padding: 0;
  }

  main {
    display: flex;
    align-items: center;
    flex-direction: column;
    align-content: center;
  }
</style>