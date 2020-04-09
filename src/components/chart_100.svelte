<script>
  import { onMount } from "svelte";
  import { getPop, randColor } from "misc";

  export let data;

  let res;
  let chart;
  res = data;
  let chart_mode = true;
  let canvasElement;
  let container_box;
  let ratio = 2.4;

  fillChart();

  //---------------
  // CHART
  //---------------
  async function fillChart() {
    await onMount(() => {
      initChart();
    });

    let length = Object.keys(res.confirmed.locations[0].history).length;
    let all = data.confirmed.locations.sort(function(a, b) {
      return b.latest - a.latest;
    });

    let i = 0,
      datasets = [],
      labels = [];

    do {
      labels.push(i);
    } while (i++ < length);

    i = 0;
    for (var country of all.slice(0, 20)) {
      let history_data = [];
      for (var [key, h] of Object.entries(country.history)) {
        if (h > 100)
          history_data.push(
            ((h * 1000000) / getPop("code", country.country_code)).toFixed(0)
          );
      }
      datasets[i++] = {
        type: "line",
        label: country.country,
        defaultFontFamily: "Open Sans",
        borderColor: randColor(),
        backgroundColor: randColor(),
        fill: false,
        data: history_data,
        yAxisID: "y-axis-1",
        pointBackgroundColor: "#1E1E21",
        pointBorderWidth: 2,
        borderWidth: 2,
        pointHitRadius: 5,
        pointRadius: 3,
        pointHoverRadius: 12,
        pointHoverBorderWidth: 3
      };
    }

    chart.type = "line";

    chart.data = {
      labels: labels,
      datasets: datasets
    };

    chart.update();
  }

  function initChart() {
    ratio = container_box.offsetWidth / (container_box.offsetHeight - 88);

    if (chart === undefined) {
      var ctx = canvasElement.getContext("2d");
      chart = new Chart(ctx, {
        type: "bar",
        data: {
          labels: [],
          datasets: []
        },
        options: {
          legend: {
            labels: {
              usePointStyle: true
            }
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
            display: false,
            text: "Charts"
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
  }
</script>

<style>
  .container-chart {
    width: 100%;
    margin-bottom: 12px;
    display: inline-block;
    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
    vertical-align: top;
  }
  .container-chart.country {
    height: calc(100vh - 348px);
  }
  .container-chart.world {
    height: calc(100% - 414px);
  }
  @media (max-width: 1280px) {
    .container-chart.world {
      height: calc(100% - 363px);
    }
  }
</style>

<div class="container-basic container-chart world" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">Countries Curves</h5>

    </div>
  </div>
  <div class="container-body">
    <canvas id="myChart" bind:this={canvasElement} />
  </div>
</div>
