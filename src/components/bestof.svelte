<script>
  import data from "jhucsse.covid";
  import * as population from "./country-by-population.json";
  import { onMount } from "svelte";

  export let type;

  let res;
  let all, all_sorted;
  
  function getContent() {
    data.all().then(function(result) {
      res = result;
      console.log(res);

      if (type == "deaths") all = res.deaths.locations;
      else if (type == "confirmed") all = res.confirmed.locations;
      else if (type == "recovered") all = res.recovered.locations;

      var all_order = [];
      all.reduce(function(res, value) {
        if (!res[value.country]) {
          res[value.country] = { country: value.country, latest: 0 };
          all_order.push(res[value.country]);
        }
        res[value.country].latest += value.latest;
        res[value.country].country_code = value.country_code;
        return res;
      }, {});

      all_sorted = all_order.sort(function(a, b) {
        return b.latest - a.latest;
      });
    });
  }
  function showList(scope){
    var el = document.querySelector('#bestof_'+scope);
    el.classList.toggle('hidden');
  }

  getContent();
  onMount(() => {
    initChart();
  });

  //---------------
  // CHART
  //---------------
  function updateConfigAsNewObject(chart) {
    chart.data = {
        labels: ['19 Mar', '20 Mar', '21 Mar', '22 Mar', '23 Mar', '24 Mar', '25 Mar','19 Mar', '20 Mar', '21 Mar', '22 Mar', '23 Mar', '24 Mar', '25 Mar'],
        datasets: [{
          label: 'Deaths',
          defaultFontFamily	:"Open Sans",
          borderColor:  "#FF4E34",
          backgroundColor: "#FF4E3426",
          fill: false,
          data: [0,1,5,7,12,18,20,31,38,39,59,60,90,102],
          yAxisID: 'y-axis-1',
          pointBorderWidth:3,
          pointHitRadius:8,
          pointRadius:6,
          pointBackgroundColor: "#1E1E21",
          pointHoverRadius:12,
          pointHoverBorderWidth:3
        }]
      };
    chart.update();
}
  function initChart() {
    console.log("LOADED");
    var ctx = document.getElementById("myChart").getContext("2d");
    var myLineChart = new Chart(ctx, {
      type: "line",
      data: {
        labels: [
          "19 Mar",
          "20 Mar",
          "21 Mar",
          "22 Mar",
          "23 Mar",
          "24 Mar",
          "25 Mar",
          "19 Mar",
          "20 Mar",
          "21 Mar",
          "22 Mar",
          "23 Mar",
          "24 Mar",
          "25 Mar"
        ],
        datasets: [
          {
            label: "Deaths",
            defaultFontFamily: "Open Sans",
            borderColor: "#FF4E34",
            backgroundColor: "#FF4E3426",
            fill: false,
            data: [0, 1, 5, 7, 12, 18, 20, 31, 38, 39, 59, 60, 90, 102],
            yAxisID: "y-axis-1",
            pointBorderWidth: 3,
            pointHitRadius: 8,
            pointRadius: 6,
            pointBackgroundColor: "#1E1E21",
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },
          {
            label: "Infected",
            defaultFontFamily: "Open Sans",
            borderColor: "#FFC831",
            backgroundColor: "#FFC83126",
            fill: false,
            data: [
              0,
              2,
              15,
              27,
              62,
              98,
              120,
              290,
              394,
              481,
              518,
              839,
              910,
              1220
            ],
            yAxisID: "y-axis-1",
            pointBorderWidth: 3,
            pointHitRadius: 8,
            pointRadius: 6,
            pointBackgroundColor: "#1E1E21",
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },
          {
            label: "Recovered",
            defaultFontFamily: "Open Sans",
            borderColor: "#40C0A5",
            backgroundColor: "#40C0A526",
            fill: false,
            data: [0, 1, 1, 2, 2, 2, 4, 7, 7, 12, 20, 22, 28, 41],
            yAxisID: "y-axis-1",
            pointBorderWidth: 3,
            pointHitRadius: 8,
            pointRadius: 6,
            pointBackgroundColor: "#1E1E21",
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          }
        ]
      },
      options: {
        responsive: true,
        hoverMode: "index",
        stacked: false,
        spanGaps: true,
        showLines: true,
        title: {
          display: false,
          text: "Chart.js Line Chart - Multi Axis"
        },
        scales: {
          yAxes: [
            {
              type: "linear", // only linear but allow scale type registration. This allows extensions to exist solely for log scale for instance
              display: true,
              position: "left",
              id: "y-axis-1",
              gridLines: {
                drawOnChartArea: false // only want the grid lines for one axis to show up
              },
              layout: {
                padding: {
                  left: 0,
                  right: 0,
                  top: 0,
                  bottom: 20
                }
              },
              legend: {
                display: true,
                labels: {
                  boxWidth: 20
                }
              },
              tooltips: {
                titleFontFamily: "Open Sans",
                titleFontSize: 15,
                bodyFontFamily: "Open Sans",
                bodyFontSize: 13
              }
            }
          ]
        }
      }
    });
  }
</script>

<style>
  .container-bestof {
    position: absolute;
    left: 84px;
    top: 100px;
    width: 380px;
    height: 560px;

    opacity: 1;
    -webkit-transition-duration: .4s;
    -moz-transition-duration: .4s;
    -o-transition-duration: .4s;
    transition-duration: .4s;
  }
  .container-bestof.hidden {
    height: 0px;
    opacity: 0;

  }
  .container-list {
    display: block;
    position: relative;
  }
  .container-list li {
    display: block;
    padding: 6px 0px;
  }
  .container-chart {
    width: 800px;
    top: 120px;
    right: 12px;
    height: 500px;
    margin-right: 0;
    position: absolute;
    transform: translateX(1000px);
    opacity: 0;
  }
  .container-chart.show {
    transform: translateX(0px);
    opacity: 1;
  }
</style>

{#if !res}
  Loading...
{:else}
  <div id="bestof_all" class="container-basic container-bestof hidden">
    <div class="container-header">
      <div class="container-header-contents">

        <h5 class="container-title">Most Infected</h5>
      </div>
    </div>
    <div class="container-body">
      <div class="container-list">
        {#each all_sorted.slice(0, 10) as item, i}
          <li>
            <label># {i + 1}</label>
            <div class="flag">
              <img
                src="flags/{item.country_code ? item.country_code : 'world'}.png"
                alt="flag" />
            </div>
            <p class="list-name">{item.country}</p>
            <p class="list-count">{item.latest}</p>
          </li>
        {/each}
      </div>
    </div>
    <div class="container-footer">
      210 countrys
      <div class="navigate-list">
        <div class="button secondary adj-left">
          <i class="fas fa-chevron-left" />
        </div>
        <div class="button secondary adj-right">
          <i class="fas fa-chevron-right" />
        </div>
      </div>
    </div>
  </div>
{/if}

<div class="container-basic container-chart">

  <div class="container-header">
    <div class="container-header-contents">

      <h5 class="container-title">Infected Evolution</h5>
    </div>
  </div>
  <div class="container-body">
    <canvas id="myChart" />
  </div>
  
</div>

<div class="container-icons">
  <div class="container-basic container-icon" on:click={() => showList("all")}>
    <i class="fas fa-procedures" />
  </div>
  <div class="container-basic container-icon">
    <i class="fas fa-notes-medical" />
  </div>
  <div class="container-basic container-icon">
    <i class="fas fa-user-times" />
  </div>
</div>