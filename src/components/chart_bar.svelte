<script>
  import { onMount } from "svelte";
  import { chart_d } from "misc";

  export let data;
  export let country;

  let res;
  let chart;
  res = data;
  let chart_mode = true;
  let canvasElement;
  let container_box;
  let ratio = 2.4;

  $: country, fillChart();

  function sort(all) {
    let all_order = [];
    let all_order2 = [];

    all.reduce(function(res, value) {
      if (!res[value.country]) {
        res[value.country] = {
          country: value.country,
          country_code: value.country_code,
          latest: 0,
          history: {}
        };
        all_order.push(res[value.country]);
      }
      res[value.country].latest += value.latest;
      let H_total = res[value.country].history;

      for (var [key, h] of Object.entries(value.history))
        H_total[key] = (H_total[key] || 0) + value.history[key];

      res[value.country].history = H_total;
      return res;
    }, {});

    Object.keys(all_order)
      .sort(function(a, b) {
        var parts_a = a.split("/");
        var parts_b = b.split("/");

        var a_date = new Date(parts_a[2], parts_a[1] - 1, parts_a[0]);
        var b_date = new Date(parts_b[2], parts_b[1] - 1, parts_b[0]);
        return new Date(b_date) - new Date(a_date);
      })
      .forEach(function(key) {
        all_order2[key] = all_order[key];
      });

    return all_order2.sort((a, b) =>
      a.country > b.country ? 1 : b.country > a.country ? -1 : 0
    );
  }

  function sort_total(dates, all) {
    var total = [];
    for (var d of dates) {
      var tot = 0;
      for (var item of all) tot += item.history[d];
      total.push(tot);
    }
    return total;
  }
  function sort_total_a(dates, all) {
    var total = [];
    for (var d of dates) {
      var tot = 0;
      for (var i = 0; i < data.confirmed.locations.length; i++) {
        tot +=
          data.confirmed.locations[i].history[d] -
          data.deaths.locations[i].history[d] -
          data.recovered.locations[i].history[d];
      }
      total.push(tot);
    }
    return total;
  }

  //---------------
  // CHART
  //---------------
  async function fillChart() {
    await onMount(() => {
      initChart();
    });
      chart.type = "line";
      
      chart.data = {
        labels: ["Day 1","Day 2", "Day 3", "Day 4","Day 5","Day 6", "Day 7", "Day 8","Day 9","Day 10"],
        datasets: [
          {
            type: "line",
            label: "Portugal",
            defaultFontFamily: "Open Sans",
            borderColor: "#FFC831",
            backgroundColor: "#FFC831",
            fill: false,
            data: [100,120, 124, 392,495,596, 650,704,894,1020],
            yAxisID: "y-axis-1",
            pointBackgroundColor: "#1E1E21",
            pointBorderWidth: 2,
            borderWidth: 2,
            pointHitRadius: 5,
            pointRadius: 3,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },{
            type: "line",
            label: "Italy",
            defaultFontFamily: "Open Sans",
            borderColor: "#34B2FF",
            backgroundColor: "#34B2FF",
            fill: false,
            data: [100,320, 893, 1203,2040,2810, 3019,4059,6048,10020],
            yAxisID: "y-axis-1",
            pointBackgroundColor: "#1E1E21",
            pointBorderWidth: 2,
            borderWidth: 2,
            pointHitRadius: 5,
            pointRadius: 3,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          }
          ,{
            type: "line",
            label: "China",
            defaultFontFamily: "Open Sans",
            borderColor: "#FF4E34",
            backgroundColor: "#FF4E34",
            fill: false,
            data: [100,2019, 6094, 9201,9012,10292, 11092,13091,14091,1903],
            yAxisID: "y-axis-1",
            pointBackgroundColor: "#1E1E21",
            pointBorderWidth: 2,
            borderWidth: 2,
            pointHitRadius: 5,
            pointRadius: 3,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },{
            type: "line",
            label: "Portugal",
            defaultFontFamily: "Open Sans",
            borderColor: "#40C0A5",
            backgroundColor: "#40C0A5",
            fill: false,
            data: [100,401, 609, 703,740,780, 810,840,860,880],
            yAxisID: "y-axis-1",
            pointBackgroundColor: "#1E1E21",
            pointBorderWidth: 2,
            borderWidth: 2,
            pointHitRadius: 5,
            pointRadius: 3,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },{
            type: "line",
            label: "France",
            defaultFontFamily: "Open Sans",
            borderColor: "#7FC040",
            backgroundColor: "#7FC040",
            fill: false,
            data: [100,301, 609, 903,1740,1780, 1810,1840,1860,1880],
            yAxisID: "y-axis-1",
            pointBackgroundColor: "#1E1E21",
            pointBorderWidth: 2,
            borderWidth: 2,
            pointHitRadius: 5,
            pointRadius: 3,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },{
            type: "line",
            label: "US",
            defaultFontFamily: "Open Sans",
            borderColor: "#D6602D",
            backgroundColor: "#D6602D",
            fill: false,
            data: [100,10192, 16049, 19003,2740,37280, 40100,52840,62860,88880],
            yAxisID: "y-axis-1",
            pointBackgroundColor: "#1E1E21",
            pointBorderWidth: 2,
            borderWidth: 2,
            pointHitRadius: 5,
            pointRadius: 3,
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          }
          ,{
            type: "line",
            label: "UK",
            defaultFontFamily: "Open Sans",
            borderColor: "#6634FF",
            backgroundColor: "#6634FF",
            fill: false,
            data: [100,12192, 18049, 19003,2140,27280, 30100,42840,48860,58880],
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

  function FormataStringData(data) {
    var dia = data.split("/")[1];
    var mes = data.split("/")[0];
    var ano = data.split("/")[2];

    return dia + "/" + ("0" + mes).slice(-2) + "/" + ano;
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
  .container-chart.world{
    height: calc(100% - 363px);
  }
}
</style>

<div
  class="container-basic container-chart {country ? 'country' : 'world'}"
  bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">Countries Curves</h5>
      
    </div>
  </div>
  <div class="container-body">
    <canvas id="myChart" bind:this={canvasElement} />
  </div>
</div>
