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
  let box_title = "Infected Count";
  let btn_text = "Growth Evolution";
  let btn_icon = "chart-line";

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
    var active_data = [];
    var recovered_data = [];
    var deaths_data = [];
    var range_dates = [];

    var growth_data = [];
    let k = 0;
    let count;
    var previous_d = 0;
    var dates = Object.keys(data.confirmed.locations[0].history).sort(function(
      a,
      b
    ) {
      return new Date(a) - new Date(b);
    });

    if (country) {
      data.confirmed.locations = sort(data.confirmed.locations);
      data.deaths.locations = sort(data.deaths.locations);
      data.recovered.locations = sort(data.recovered.locations);

      var confirmed = data.confirmed.locations.filter(
        e => country === e.country_code
      )[0];
      var recovered = data.recovered.locations.filter(
        e => country === e.country_code
      )[0];
      var deaths = data.deaths.locations.filter(
        e => country === e.country_code
      )[0];

      if (confirmed) {
        var i = 0;
        for (var d of dates) {
          if (confirmed.history[d] != 0) {
            range_dates.push(chart_d(d));
            deaths_data.push(deaths.history[d]);
            recovered_data.push(recovered.history[d]);
            active_data.push(
              confirmed.history[d] - deaths.history[d] - recovered.history[d]
            );
            if (k > 0) {
              let growth = parseInt(
                ((confirmed.history[d] - confirmed.history[previous_d]) * 100) /
                  confirmed.history[previous_d]
              );
              growth_data.push(growth);
            } else {
              growth_data.push(0);
            }
            previous_d = d;
            k++;
          }

          i++;
        }
      }
    } else {
      for (var d of dates) range_dates.push(chart_d(d));
      deaths_data = sort_total(dates, data.deaths.locations);
      recovered_data = sort_total(dates, data.recovered.locations);
      active_data = sort_total_a(dates);

      for (var d of dates) {
        if (k > 0) {
          let growth = parseInt(
            ((active_data[k] - active_data[k - 1]) * 100) / active_data[k - 1]
          );
          growth_data.push(growth);
        } else {
          growth_data.push(0);
        }
        previous_d = d;
        k++;
      }
    }
    if (chart_mode) {
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
    } else {
      chart.data = {
        labels: range_dates,
        datasets: [
          {
            label: "Growth %",
            defaultFontFamily: "Open Sans",
            borderColor: "#FFC831",
            backgroundColor: "#FFC83126",
            fill: false,
            data: growth_data,
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
    }
    chart.update();
  }

  function initChart() {
    if (chart === undefined) {
      var ctx = canvasElement.getContext("2d");
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
          tooltips: {
            mode: "index"
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
  }

  function FormataStringData(data) {
    var dia = data.split("/")[1];
    var mes = data.split("/")[0];
    var ano = data.split("/")[2];

    return dia + "/" + ("0" + mes).slice(-2) + "/" + ano;
  }

  function changeChart(e) {
    if (chart_mode) {
      chart_mode = false;
      fillChart(chart, data, false);
      btn_text = "Infected Count";
      box_title = "Growth Evolution";
      btn_icon = "user-friends";
    } else {
      chart_mode = true;
      fillChart(chart, data, true);
      box_title = "Infected Count";
      btn_text = "Growth Evolution";
      btn_icon = "chart-line";
    }
    //fillChart(chart, data);
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

      <h5 class="container-title">{box_title}</h5>
      <div style="float:right" class="button" on:click={() => changeChart()}>
        <i class="fas fa-{btn_icon}" />
        <p>{btn_text}</p>
      </div>
    </div>
  </div>
  <div class="container-body">
    <canvas id="myChart" bind:this={canvasElement} />
  </div>
</div>
