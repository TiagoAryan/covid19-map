<script>
  import { onMount } from "svelte";
  import * as country_by_flag from "./country-by-flag.json";
  import * as averagecountryAge from "./country-average-age.json";

  export let data;

  let res, dates;
  var chart;
  var c_infected = [];
  var c_healthy = [];
  var myScaterChart;

  onMount(async () => {
    initChartPoint();
  });

  res = data;

  dates = Object.keys(res.confirmed.locations[0].history).sort(function(a, b) {
    return new Date(a) - new Date(b);
  });

  res.confirmed.locations = sort(res.confirmed.locations);
  res.deaths.locations = sort(res.deaths.locations);
  res.recovered.locations = sort(res.recovered.locations);

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
        H_total[FormataStringData(key)] =
          (H_total[FormataStringData(key)] || 0) + value.history[key];

      res[value.country].history = H_total;
      return res;
    }, {});

    Object.keys(all_order)
      .sort(function(a, b) {
        var parts_a = a.split("/");
        var parts_b = b.split("/");

        var a_date = new Date(parts_a[2], parts_a[1] - 1, parts_a[0]);
        var b_date = new Date(parts_b[2], parts_b[1] - 1, parts_b[0]);
        return new Date(parts_b) - new Date(a_date);
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

  function initChartPoint() {
    var fatality_data = [
      {
        x: 45.5,
        y: 12.3
      },
      {
        x: 41.4,
        y: 10.0
      },
      {
        x: 42.7,
        y: 9.39
      },
      {
        x: 38.1,
        y: 1.08
      },
      {
        x: 40.5,
        y: 9.33
      },
      {
        x: 42.2,
        y: 2.4
      },
      {
        x: 37.4,
        y: 4.03
      }
    ];
    var label_data = ["it", "fr", "ES", "US", "UK", "pt", "CH"];
    var title_data = [
      "Italy",
      "France",
      "Spain",
      "United States",
      "United Kingdom",
      "Portugal",
      "China"
    ];

    var ctx = document.getElementById("myChartPoint").getContext("2d");
    myScaterChart = new Chart(ctx, {
      type: "scatter",
      data: {
        labels: [20, 30, 40, 50],
        datasets: [
          {
            label: "Hight Fatality for Average Age",
            borderColor: "#FF4E34",
            backgroundColor: "#FF4E3426",
            data: fatality_data,
            pointBorderWidth: 2,
            pointHitRadius: 4,
            pointRadius: 4,
            pointBackgroundColor: "#1E1E21",
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          }
        ]
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
        tooltips: {
          enabled: false,
          xPadding: 8,
          yPadding: 8,
          backgroundColor: "rgba(0,0,0,0.8)",

          custom: function(tooltipModel) {
            var tooltipEl = document.getElementById("chartjs-tooltip");
            // Create element on first render
            if (!tooltipEl) {
              tooltipEl = document.createElement("div");
              tooltipEl.id = "chartjs-tooltip";
              tooltipEl.innerHTML = "<table></table>";
              document.body.appendChild(tooltipEl);
            }

            // Hide if no tooltip
            if (tooltipModel.opacity === 0) {
              tooltipEl.style.opacity = 0;
              return;
            }
            // Set caret Position
            tooltipEl.classList.remove("above", "below", "no-transform");
            if (tooltipModel.yAlign) {
              tooltipEl.classList.add(tooltipModel.yAlign);
            } else {
              tooltipEl.classList.add("no-transform");
            }
            function getBody(bodyItem) {
              return bodyItem.lines;
            }

            // Set Text
            if (tooltipModel.body) {
              var index = tooltipModel.dataPoints[0].index;
              var titleLines = tooltipModel.title || [];
              var bodyLines = tooltipModel.body.map(getBody);
              var fatality = tooltipModel.dataPoints[0].value;
              var averageAge = tooltipModel.dataPoints[0].label;

              console.log(tooltipModel);

              var innerHtml = "<thead>";

              innerHtml +=
                "<tr><th><div class='flag' style='margin-right:8px'><img src='" +
                country_by_flag.default.filter(
                  e => title_data[index] === e.country
                )[0].flag_base64 +
                "' alt='flag' /></div>" +
                title_data[index] +
                "</th></tr>";

              innerHtml += "</thead><tbody>";

              innerHtml +=
                "<tr><th> <div style='width:80px; display:inline-block; opacity:0.4; font-weight:300'>Average Age</div> <div style='display:inline-block; width:40px; text-align:right; font-size:14px'>" +
                averageAge;
              "</div></th></tr>";
              innerHtml +=
                "<tr><th> <div style='width:80px; display:inline-block; opacity:0.4; font-weight:300'>Fatality Rate</div> <div style='display:inline-block; width:40px; text-align:right; font-size:14px'>" +
                fatality;
              "</div></th></tr>";

              innerHtml += "</tbody>";

              var tableRoot = tooltipEl.querySelector("table");
              tableRoot.innerHTML = innerHtml;
            }

            // `this` will be the overall tooltip
            var position = this._chart.canvas.getBoundingClientRect();

            // Display, position, and set styles for font
            tooltipEl.style.opacity = 1;
            tooltipEl.style.position = "absolute";
            tooltipEl.style.left =
              position.left + window.pageXOffset + tooltipModel.caretX + "px";
            tooltipEl.style.top =
              position.top + window.pageYOffset + tooltipModel.caretY + "px";
            tooltipEl.style.fontFamily = tooltipModel._bodyFontFamily;
            tooltipEl.style.fontSize = tooltipModel.bodyFontSize + "px";
            tooltipEl.style.fontStyle = tooltipModel._bodyFontStyle;
            tooltipEl.style.padding =
              tooltipModel.yPadding + "px " + tooltipModel.xPadding + "px";
            tooltipEl.style.pointerEvents = "none";
          }
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

    return ("0" + dia).slice(-2) + "/" + ("0" + mes).slice(-2) + "/" + ano;
    // Utilizo o .slice(-2) para garantir o formato com 2 digitos.
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

      <h5 class="container-title">Fatality Rate</h5>
    </div>
  </div>
  <div class="container-body">
    <canvas id="myChartPoint" />
  </div>
</div>
