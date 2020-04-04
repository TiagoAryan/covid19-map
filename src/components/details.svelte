<script>
  import Preview from "../components/preview.svelte";
  import Countries from "../components/countries.svelte";
  import Chart from "../components/chart.svelte";
  import ChartPoints from "../components/chart_points.svelte";

  export let data;
  export let bounds;
  export let country;
  export let name;
</script>

<style>
  .container_details_box {
    width: calc(100vw - 18px);
    top: 100px;
    height: calc(100vh - 24px);

    left: 12px;
    top: 12px;
    display: inline;
    padding-bottom: 0px;
    margin-right: 0;
    position: absolute;
    transform: translateX(0px);
    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }
  .container_details_box-half {
    left: auto;
    width: calc(50vw - 18px);
    right: 12px;
  }
  .container_details_box.hidden {
    transform: translateY(100vh);
    opacity: 0;
  }
  .container_details {
    width: calc(100vw - 18px);
    height: calc(100vh - 24px);
    position: relative;
    display: block;
  }

  .box-half {
    width: calc(50% - 6px);
    display: inline-block;
    height: 100%;
    position: relative;
    vertical-align: top;
  }
  .box-half-right {
    margin-left: 6px;
  }
</style>

<div
  class="container_details_box {country ? 'container_details_box-half' : ''}
  hidden">
  <div class="container_details">
    {#if !country}
      <div class="box-half">
        <Preview {data} {country} {name} />
        <Countries {data} {bounds} />
      </div>
    {/if}

    <div class="box-half box-half-right">
      {#if country}
        <Preview {data} {country} {name} />
      {/if}
      <Chart {data} {country} />
      {#if !country}
        <ChartPoints {data} {country} />
      {/if}
    </div>
  </div>
</div>
