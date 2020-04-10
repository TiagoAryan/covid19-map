<script>
  import getCountryISO2 from "country-iso-3-to-2";
  import { flag } from "misc";
  import SvelteTooltip from "svelte-tooltip";
  import { onMount } from "svelte";

  export let data;
  export let bounds;

  let res;
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
    
async function fillChart() {
    await onMount(() => {
      initChart();
    });
    
    var total_countrys= c_infected.length+c_healthy.length;

    chart.data = {
				datasets: [{
					data:[c_healthy.length, c_infected.length],
          backgroundColor: ["#40C0A526","#FFC83126"],
          borderColor:[ "#40C0A5", "#FFC831"],
          
					label: ''
				}],
				labels: ["Healthy", "Infected"]
      };
      /*
      chart.data = {
        labels: ["Healthy", "Infected"],
        datasets: [
          {
            label: "Healthy",
            defaultFontFamily: "Open Sans",
            borderColor:[ "#40C0A5", "#FFC831"],
            backgroundColor: ["#40C0A526","#FFC83126"],
            fill: false,
            data: [c_infected.length, c_healthy.length],
            yAxisID: "y-axis-1",
            pointBackgroundColor: "#1E1E21",
            pointBorderWidth: 2,
            borderWidth: 2,
            pointHitRadius: 5,
            pointRadius: 3,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          }
        ]
      };
      */
      chart.update();
  }
  
  function initChart() {
    ratio = container_box.offsetWidth / (container_box.offsetHeight );
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
            display:false

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
          },

        }
      });
    }
  }

  function changeChart(e) {
    if (chart_mode) {
      chart_mode = false;
      fillChart();
      btn_text = "Infected Count";
      box_title = "Growth Evolution";
      btn_icon = "user-friends";
    } else {
      chart_mode = true;
      fillChart();
      box_title = "Infected Count";
      btn_text = "Growth Evolution";
      btn_icon = "chart-line";
    }
    //fillChart(chart, data);
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
  .container-list label {
    width: 40px;
  }
  .container-list .flag {
    width: 28px;
  }
  .flag {
    transform: scale(0.9);
    opacity: 0.9;

    -webkit-transition-duration: 0.2s;
    -moz-transition-duration: 0.2s;
    -o-transition-duration: 0.2s;
    transition-duration: 0.2s;
  }
  .flag:hover {
    transform: scale(1.1);
    opacity: 1;
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
    width: 49%;
    vertical-align: top;
    height: 100%;
  }
  .container-body {
    overflow: scroll;
    height: 100%;
    padding: 8px 24px;
  }
  .container-basic {
    height: 170px;
    margin-bottom: 6px;
  }
  .container-chart, .container-info{
    width: 64%;
    display: inline-block;
    height: 100%;
    vertical-align: top;
  }
  .container-chart{
    width: 35%;
  }
  .label{
    width: 100%;
    margin-bottom:12px;
    display: block;
  }
  .percentage{
    float: right;
    display: inline-block;
    background-color: rgba(255,255,255,0.1);
    color: white;
    border-radius: 3px;
    padding: 2px 6px;
    font-size: 0.8rem;
    margin-left:12px;

  }
  .container-info p{
    margin: 0px
  }
  .dot{
    transform: translateY(5px);
  }
  @media (max-width: 1280px) {
    .container-basic {
      height: 140px;
    }
  }
</style>

{#if !res}
  Loading...
{:else}
  <div class="container-basic container-countries ">
      <div class="container-body">
        <div class="container-chart" bind:this={container_box}>
          <canvas id="myChart" bind:this={canvasElement} />
       </div>
        <div class="container-info">
          <div class="label">
            <i class="dot dot_yellow" />
            <p class="container-title">Infected Countries</p>
            <div class="percentage">{parseInt(c_infected.length/(c_infected.length+c_healthy.length)*100)}%</div>
            <h5 style="float:right">{c_infected.length}</h5>
          </div>
          <div class="label">
            <i class="dot dot_green" />
            <p class="container-title">Healthy Countries</p>
            <div class="percentage">{parseInt(c_healthy.length/(c_infected.length+c_healthy.length)*100)}%</div>
            <h5 style="float:right">{c_healthy.length}</h5>
          </div>

        </div>
      </div>
  </div>
{/if}
