<script>
  import data from "jhucsse.covid";
  import * as population from "./country-by-population.json";
  import { onMount } from "svelte";
  import getCountryISO2 from "country-iso-3-to-2";
  import Preview from "../components/preview.svelte";
  import Countries from "../components/countries.svelte";
  import Chart from "../components/chart.svelte";
  import ChartPoints from "../components/chart_points.svelte";

  export let type;
  export let country;
  let country_clicked, country_name_clicked;

  let res, dates;
  var chart;
  var c_infected = [];
  var c_healthy = [];
</script>

<style>
  .container_details_box {
    width: calc(100vw - 18px);
    top: 100px;
    height: calc(100vh - 112px);

    left: 12px;
    display: inline;
    padding-bottom: 30px;
    margin-right: 0;
    position: absolute;
    transform: translateX(0px);
    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }
  .container_details_box.hidden {
    transform: translateY(100vh);
    opacity: 0;
  }
  .container_details {
    width: calc(100vw - 18px);
    height: calc(100vh - 112px);
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

<div class="container_details_box hidden">
  <div class="container_details">
    <div class="box-half">
      <Preview country={country_clicked} name={country_name_clicked} />
      <Countries />

    </div>
    <div class="box-half box-half-right">
      <Chart type="confirmed" country={country_clicked} />
      <ChartPoints country={country_clicked} />

    </div>
  </div>
</div>
