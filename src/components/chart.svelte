<script>
  import { onMount } from "svelte";

  export let data;
  export let country;

  let res;
  var chart;
  res = data;

  $: country && fillChart(chart, data);
  onMount(() => {
    initChart();
  });

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

  //---------------
  // CHART
  //---------------
  function fillChart(chart, country_data) {
    var dates = Object.keys(data.confirmed.locations[0].history).sort(function(
      a,
      b
    ) {
      return new Date(a) - new Date(b);
    });

    country_data.confirmed.locations = sort(country_data.confirmed.locations);
    country_data.deaths.locations = sort(country_data.deaths.locations);
    country_data.recovered.locations = sort(country_data.recovered.locations);

    var confirmed = country_data.confirmed.locations.filter(
      e => country === e.country_code
    )[0];
    var recovered = country_data.recovered.locations.filter(
      e => country === e.country_code
    )[0];
    var deaths = country_data.deaths.locations.filter(
      e => country === e.country_code
    )[0];

    var active_data = [];
    var recovered_data = [];
    var deaths_data = [];
    var range_dates = [];
    let k = 0;
    for (var d of dates) {
      if (confirmed.history[d] != 0) {
        range_dates.push(d);
        deaths_data.push(deaths.history[d]);
        recovered_data.push(recovered.history[d]);
        active_data.push(
          confirmed.history[d] - deaths.history[d] - recovered.history[d]
        );
      }
    }

    chart.data = {
      labels: range_dates,
      datasets: [
        {
          label: "Deaths",
          defaultFontFamily: "Open Sans",
          borderColor: "#FF4E34",
          backgroundColor: "#FF4E3426",
          fill: false,
          data: deaths_data,
          yAxisID: "y-axis-1",
          pointBackgroundColor: "#1E1E21",
          pointBorderWidth: 2,
          borderWidth: 2,
          pointHitRadius: 5,
          pointRadius: 3,
          pointHoverRadius: 12,
          pointHoverBorderWidth: 3
        },
        {
          label: "Active",
          defaultFontFamily: "Open Sans",
          borderColor: "#FFC831",
          backgroundColor: "#FFC83126",
          fill: false,
          data: active_data,
          yAxisID: "y-axis-1",
          pointBackgroundColor: "#1E1E21",
          pointBorderWidth: 2,
          borderWidth: 2,
          pointHitRadius: 5,
          pointRadius: 3,
          pointHoverRadius: 12,
          pointHoverBorderWidth: 3
        },
        {
          label: "Recovered",
          defaultFontFamily: "Open Sans",
          borderColor: "#40C0A5",
          backgroundColor: "#40C0A526",
          fill: false,
          data: recovered_data,
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

  function initChart(country_data) {
    var ctx = document.getElementById("myChart").getContext("2d");
    chart = new Chart(ctx, {
      type: "line",
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
        aspectRatio: 2.4,
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

  function FormataStringData(data) {
    var dia = data.split("/")[1];
    var mes = data.split("/")[0];
    var ano = data.split("/")[2];

    return dia + "/" + ("0" + mes).slice(-2) + "/" + ano;
  }
</script>

<style>
  .container-chart {
    width: calc(100% - 12px);
    height: calc(50% - 6px);
    margin-bottom: 12px;
    display: inline-block;
    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
    vertical-align: top;
  }
</style>

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
