<script>
  import getCountryISO2 from "country-iso-3-to-2";
  import { flag, s } from "misc";
  import { onMount } from "svelte";

  export let data;
  export let bounds;
  let pop_total = 0,
    deaths = 0,
    confirmed = 0,
    recovered = 0,
    fatality = 0,
    critical_per = 0,
    critical = 0,
    tests = 0;

  let res;
  let confirmed_data, deaths_data, recovered_data;
  var c_infected = [];
  var c_healthy = [];
  let canvasElement;
  let container_box;
  let ratio = 1;

  let chart;

  res = data;
  let all = res.confirmed.locations;
  var all_order = [];

  $: fillChart();

  async function fillChart() {
    all.reduce(function(res, value) {
      if (!res[value.country]) {
        res[value.country] = {
          country: value.country,
          country_code: value.country_code
        };
        all_order.push(res[value.country]);
      }
      return res;
    }, {});

    for (var country of Object.entries(bounds)) {
      if (
        res.confirmed.locations.find(
          e => e.country_code === getCountryISO2(country[1].id)
        ) == undefined
      )
        c_healthy.push([country[0], getCountryISO2(country[1].id)]);
      else c_infected.push([country[0], getCountryISO2(country[1].id)]);
    }

    deaths = res.latest.deaths;
    confirmed = res.latest.confirmed;
    recovered = res.latest.recovered;
    critical = res.latest.critical;
    tests = res.latest.tests;

    pop_total = confirmed;

    critical_per = (critical / (confirmed - deaths - recovered)) * 100;
    fatality = (deaths / confirmed) * 100;
    await onMount(() => {
      initChart();
    });

    var total_countrys = c_infected.length + c_healthy.length;

    chart.data = {
      datasets: [
        {
          data: [c_healthy.length, c_infected.length],
          backgroundColor: ["#40C0A526", "#FFC83126"],
          borderColor: ["#40C0A5", "#FFC831"],

          label: ""
        }
      ],
      labels: ["Healthy", "Infected"]
    };
    chart.update();
  }

  function initChart() {
    ratio = container_box.offsetWidth / container_box.offsetHeight;
    if (chart === undefined) {
      var ctx = canvasElement.getContext("2d");
      chart = new Chart(ctx, {
        type: "pie",
        data: {
          labels: [],
          datasets: []
        },
        options: {
          legend: {
            labels: {
              usePointStyle: true
            },
            display: false
          },
          tooltips: {
            mode: "index"
          },
          aspectRatio: ratio,
          responsive: true,
          hoverMode: "index",
          stacked: false,
          spanGaps: true,
          showLines: true,
          title: {
            display: false
          }
        }
      });
    }
  }
</script>

<style>
  .container-countries {
    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }

  .container-basic {
    width: 100%;
    height: calc(100% - 202px);
    display: inline-block;
    position: relative;
    overflow: hidden;
  }
  .container-list-group {
    display: inline-block;
    width: 44%;
    vertical-align: top;
    height: 100%;
    position: relative;
  }
  .container-body {
    overflow: scroll;
    height: 100%;
    padding: 0px 20px;
  }
  .container-basic {
    height: 140px;
    margin-bottom: 6px;
  }
  .container-chart,
  .container-info {
    width: 45%;
    display: inline-block;
    height: 100%;
    vertical-align: top;
  }
  .container-chart {
    width: 50%;
    padding: 0px;
    height: calc(100% - 50px);
  }
  .label {
    width: 100%;
    margin-bottom: 0px;
    display: block;
  }
  .percentage {
    float: right;
    display: inline-block;
    background-color: rgba(255, 255, 255, 0.1);
    color: white;
    border-radius: 3px;
    padding: 2px 6px;
    font-size: 0.8rem;
    margin-left: 12px;
  }
  .container-chart-label {
    margin-bottom: 0px;
  }
  .dot {
    transform: translateY(5px);
  }
  .col-block {
    width: 18%;
    padding-top: 20px;
  }

  h4 {
    margin: 0px;
  }
  .label-big h4 {
    display: inline-block;
    margin: 0;
    font-weight: 600;
  }
  .label-big {
    padding: 6px 12px;
    border-radius: 6px;
    display: inherit;
  }
  .label-yellow {
    color: #ffc831;
    background-color: rgba(255, 200, 49, 0.2);
  }
  .label-red {
    color: #ff4e34;
    background-color: rgba(255, 78, 52, 0.2);
  }
  .label-green {
    color: #40c0a5;
    background-color: rgba(64, 192, 165, 0.2);
  }
  .col-block label {
    display: block;
    margin-bottom: 4px;
  }
  @media (max-width: 1280px) {
    .container-basic {
      height: 140px;
    }
  }
  .container-title {
    margin-bottom: 12px;
    display: block;
  }
  @media (max-width: 768px) {
    .container-basic {
      height: auto;
    }
    .container-chart {
      height: 80px;
    }
    .col-block {
      width: 18%;
      padding-top: 20px;
      padding-left: 20px;
    }
    .container-basic {
      margin-bottom: 0px;
    }
  }
  @media (max-width: 480px) {
    .container-list-group {
      width: 100%;
    }
    .col-block {
      width: 31%;
      padding-top: 20px;
      padding-left: 20px;
    }
    .container-chart {
      height: 70px;
    }
  }
</style>

{#if !res}
  Loading...
{:else}
  <div class="container-basic container-countries ">
    <div class="container-list-group">
      <div class="container-body">
        <h5 class="container-title">World Overview</h5>

        <div class="container-chart" bind:this={container_box}>
          <canvas id="myChart" bind:this={canvasElement} />
        </div>
        <div class="container-info">
          <div class="label">
            <i class="dot dot_yellow" />
            <label class="container-chart-label">Country Infected</label>
          </div>
          <div class="label">
            <i class="dot dot_green" />
            <label class="container-chart-label">Country Healthy</label>
          </div>

        </div>
      </div>
    </div>
    <div class="col-block">
      <label>Tests</label>
      <div
        class="label-big {(tests / pop_total) * 1000 > 10 ? 'label-green' : ''}
        {(tests / pop_total) * 1000 > 5 && (tests / pop_total) * 1000 <= 10 ? 'label-yellow' : ''}
        {(tests / pop_total) * 1000 < 5 ? 'label-red' : ''}
        ">
        <h4>
          {parseInt(tests / 1000000) > 0 ? s(parseInt(tests / 1000000)) + 'M' : ''}
          {parseInt(tests / 1000) > 0 && parseInt(tests / 1000000) <= 0 ? s(parseInt(tests / 1000)) + 'k' : ''}
          {parseInt(tests / 1000) <= 0 ? s(tests) : ''}
        </h4>

      </div>
    </div>

    <div class="col-block">
      <label>In Critical State</label>
      <div
        class="label-big {critical_per > 7 ? 'label-red' : ''}
        {critical_per > 3.5 && critical_per <= 7 ? 'label-yellow' : ''}
        {critical_per < 5 ? 'label-green' : ''}
        ">
        <h4>{s(critical)}</h4>
      </div>
    </div>
    <div class="col-block">
      <label>Fatality</label>
      <div
        class="label-big {fatality > 10 ? 'label-red' : ''}
        {fatality > 5 && fatality <= 10 ? 'label-yellow' : ''}
        {fatality < 5 ? 'label-green' : ''}
        ">

        <h4>{fatality.toFixed(1)}%</h4>
      </div>
    </div>
  </div>
{/if}
