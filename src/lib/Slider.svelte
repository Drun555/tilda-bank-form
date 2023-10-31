<script>
    import Range from "./Range.svelte"	

    export let min = 1;
    export let max = 100;

    export let minName = min.toString();
    export let maxName = max.toString();

    export let step = 1;

    export let value = max / 2;
</script>


<div class="green-theme">
  
    <Range min={min} max={max} step={step} bind:value={value} on:change={(e) => value = e.detail.value} />

    <div class='sliderNames'>
      <h4>
        {minName}
      </h4>
      <h4>
        {maxName}
      </h4>
    </div>
</div>

<style>
  .green-theme {
    --track-height: 1px;
		--track-focus: #05E69F;
		--track-highlight-bgcolor: #05E69F;
		--track-highlight-bg: linear-gradient(90deg, #05d191, #05E69F);
		--thumb-holding-outline: rgba(102, 251, 134, 0.3);
    --thumb-bgcolor: #05E69F;
    --thumb-boxshadow: none;
  }

  div {
    width: 100%;
  }

  h4 {
    color: #A6A9AF;
  }

  .sliderNames {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    
  }

</style>