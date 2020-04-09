<script>
  import { onMount } from "svelte";
  import * as avgage from "./avgage.json";
  import { flag } from "misc";

  export let data;

  let res, dates;
  var chart;
  var myScaterChart;
  var fatality_data_red = [];
  var fatality_data_yellow = [];
  var fatality_data_green = [];
  var title_data_red = [];
  var title_data_yellow = [];
  var title_data_green = [];
  let container_box;
  let ratio = 2.4;
  let canvasElement;

  onMount(async () => {
    initChartPoint();
  });

  let all = data.confirmed.locations.sort(function(a, b) {
    return b.latest - a.latest;
  });

  for (var country of all.slice(0, 100)) {
    var fatality_rate = (
      (data.deaths.locations.filter(
        e => country.country_code === e.country_code
      )[0].latest *
        100) /
      country.latest
    ).toFixed(2);
    var average_age = avgage.default[country.country];
    if (average_age / fatality_rate < 4) {
      fatality_data_red.push({
        x: average_age,
        y: fatality_rate
      });
      title_data_red.push(country.country);
    } else if (average_age / fatality_rate < 16) {
      fatality_data_yellow.push({
        x: average_age,
        y: fatality_rate
      });
      title_data_yellow.push(country.country);
    } else {
      fatality_data_green.push({
        x: average_age,
        y: fatality_rate
      });
      title_data_green.push(country.country);
    }
  }

  //---------------
  // CHART
  //---------------

  function initChartPoint() {
    ratio = container_box.offsetWidth / (container_box.offsetHeight - 88);

    var ctx = canvasElement.getContext("2d");
    myScaterChart = new Chart(ctx, {
      type: "scatter",
      data: {
        labels: [20, 30, 40, 50],
        datasets: [
          {
            label: "Hight",
            borderColor: "#FF4E34",
            backgroundColor: "#FF4E3426",
            data: fatality_data_red,
            pointBorderWidth: 2,
            pointHitRadius: 4,
            pointRadius: 4,
            pointBackgroundColor: "#1E1E21",
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },
          {
            label: "Medium ",
            borderColor: "#FFC831",
            backgroundColor: "#FFC83126",
            data: fatality_data_yellow,
            pointBorderWidth: 2,
            pointHitRadius: 4,
            pointRadius: 4,
            pointBackgroundColor: "#1E1E21",
            pointHoverRadius: 12,
            pointHoverBorderWidth: 3
          },
          {
            label: "Normal",
            borderColor: "#40C0A5",
            backgroundColor: "#40C0A526",
            data: fatality_data_green,
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
        aspectRatio: ratio,
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
              tooltipEl.style.backgroundColor = "rgba(0,0,0,0.8)";
              tooltipEl.style.borderRadius = "4px";
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
              var dataindex = tooltipModel.dataPoints[0].datasetIndex;
              var index = tooltipModel.dataPoints[0].index;
              var titleLines = tooltipModel.title || [];
              var bodyLines = tooltipModel.body.map(getBody);
              var fatality = tooltipModel.dataPoints[0].value;
              var averageAge = tooltipModel.dataPoints[0].label;

              let title_data;
              if (dataindex == 0) {
                title_data = title_data_red;
              } else if (dataindex == 1) {
                title_data = title_data_yellow;
              } else {
                title_data = title_data_green;
              }
              var innerHtml = "<thead>";
              innerHtml +=
                "<tr><th><div class='flag' style='margin-right:8px'><img src='" +
                flag(title_data[index]) +
                "' alt='flag' /></div>" +
                title_data[index] +
                "</th></tr>";

              innerHtml += "</thead><tbody>";

              innerHtml +=
                "<tr><th> <div style='width:80px; display:inline-block; opacity:0.4; font-weight:300'>Average Age</div> <div style='font-weight:400; display:inline-block; width:40px; text-align:right; font-size:15px'>" +
                averageAge +
                "</div></th></tr>";
              innerHtml +=
                "<tr><th> <div style='width:80px; display:inline-block; opacity:0.4; font-weight:300'>Fatality Rate</div> <div style='font-weight:400; display:inline-block; width:40px; text-align:right; font-size:15px'>" +
                round(fatality, 1) +
                "%</div></th></tr>";

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

  function round(value, precision) {
    var multiplier = Math.pow(10, precision || 0);
    return Math.round(value * multiplier) / multiplier;
  }
</script>

<style>
  .container-chart {
    width: 100%;
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
  label {
    margin: 0;
  }
  .container-header {
    padding-bottom: 10px;
  }
</style>

<div class="container-basic container-chart" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">Fatality related to Average Age</h5>
      <label style="display:block">50 countries with more Deaths</label>
    </div>
  </div>
  <div class="container-body">
    <canvas id="myChartPoint" bind:this={canvasElement} />
  </div>
</div>
