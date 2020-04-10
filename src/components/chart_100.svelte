<script>
  import { onMount } from "svelte";
  import { getPop } from "misc";

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
    for (var country of all.slice(0, 10)) {
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
        borderColor: materialColor(),
        backgroundColor: materialColor(),
        fill: false,
        data: history_data,
        yAxisID: "y-axis-1",
        pointBackgroundColor: "#1E1E21",
        pointBorderWidth: 2,
        borderWidth: 2,
        pointHitRadius: 3,
        pointRadius: 2,
        pointHoverRadius: 8,
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
            mode: "point"
          },
          aspectRatio: ratio,

          responsive: true,
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
  function pickRandomProperty(obj) {
    var result;
    var count = 0;
    for (var prop in obj) if (Math.random() < 1 / ++count) result = prop;
    return result;
  }

  function materialColor() {
    // colors from https://github.com/egoist/color-lib/blob/master/color.json
    var colors = {
      red: {
        "400": "#ef5350",
        "600": "#e53935",
        "800": "#c62828",
        hex: "#f44336",
        a100: "#ff8a80",
        a200: "#ff5252",
        a400: "#ff1744",
        a700: "#d50000"
      },
      pink: {
        "400": "#ec407a",
        "600": "#d81b60",
        "800": "#ad1457",
        hex: "#e91e63",
        a100: "#ff80ab",
        a200: "#ff4081",
        a400: "#f50057",
        a700: "#c51162"
      },
      purple: {
        "400": "#ab47bc",
        "600": "#8e24aa",
        "800": "#6a1b9a",
        hex: "#9c27b0",
        a100: "#ea80fc",
        a200: "#e040fb",
        a400: "#d500f9",
        a700: "#aa00ff"
      },
      deepPurple: {
        "400": "#7e57c2",
        "600": "#5e35b1",
        "800": "#4527a0",
        hex: "#673ab7",
        a100: "#b388ff",
        a200: "#7c4dff",
        a400: "#651fff",
        a700: "#6200ea"
      },
      indigo: {
        "400": "#5c6bc0",
        "600": "#3949ab",
        "800": "#283593",
        hex: "#3f51b5",
        a100: "#8c9eff",
        a200: "#536dfe",
        a400: "#3d5afe",
        a700: "#304ffe"
      },
      blue: {
        "400": "#42a5f5",
        "600": "#1e88e5",
        "800": "#1565c0",
        hex: "#2196f3",
        a100: "#82b1ff",
        a200: "#448aff",
        a400: "#2979ff",
        a700: "#2962ff"
      },
      lightBlue: {
        "400": "#29b6f6",
        "600": "#039be5",
        "800": "#0277bd",
        hex: "#03a9f4",
        a100: "#80d8ff",
        a200: "#40c4ff",
        a400: "#00b0ff",
        a700: "#0091ea"
      },
      cyan: {
        "400": "#26c6da",
        "600": "#00acc1",
        "800": "#00838f",
        hex: "#00bcd4",
        a100: "#84ffff",
        a200: "#18ffff",
        a400: "#00e5ff",
        a700: "#00b8d4"
      },
      teal: {
        "400": "#26a69a",
        "600": "#00897b",
        "800": "#00695c",
        hex: "#009688",
        a100: "#a7ffeb",
        a200: "#64ffda",
        a400: "#1de9b6",
        a700: "#00bfa5"
      },
      green: {
        "400": "#66bb6a",
        "600": "#43a047",
        "800": "#2e7d32",
        hex: "#4caf50",
        a100: "#b9f6ca",
        a200: "#69f0ae",
        a400: "#00e676",
        a700: "#00c853"
      },
      lightGreen: {
        "400": "#9ccc65",
        "600": "#7cb342",
        "800": "#558b2f",
        hex: "#8bc34a",
        a100: "#ccff90",
        a200: "#b2ff59",
        a400: "#76ff03",
        a700: "#64dd17"
      },
      lime: {
        "400": "#d4e157",
        "600": "#c0ca33",
        "800": "#9e9d24",
        hex: "#cddc39",
        a100: "#f4ff81",
        a200: "#eeff41",
        a400: "#c6ff00",
        a700: "#aeea00"
      },
      yellow: {
        "400": "#ffee58",
        "600": "#fdd835",
        "800": "#f9a825",
        hex: "#ffeb3b",
        a100: "#ffff8d",
        a200: "#ffff00",
        a400: "#ffea00",
        a700: "#ffd600"
      },
      amber: {
        "400": "#ffca28",
        "600": "#ffb300",
        "800": "#ff8f00",
        hex: "#ffc107",
        a100: "#ffe57f",
        a200: "#ffd740",
        a400: "#ffc400",
        a700: "#ffab00"
      },
      orange: {
        "400": "#ffa726",
        "600": "#fb8c00",
        "800": "#ef6c00",
        hex: "#ff9800",
        a100: "#ffd180",
        a200: "#ffab40",
        a400: "#ff9100",
        a700: "#ff6d00"
      },
      deepOrange: {
        "400": "#ff7043",
        "600": "#f4511e",
        "800": "#d84315",
        hex: "#ff5722",
        a100: "#ff9e80",
        a200: "#ff6e40",
        a400: "#ff3d00",
        a700: "#dd2c00"
      },
      blueGrey: {
        "400": "#78909c",
        "600": "#546e7a",
        "800": "#37474f",
        hex: "#607d8b"
      }
    };
    // pick random property
    //var property = pickRandomProperty(colors);
    var colorList = colors[pickRandomProperty(colors)];
    var newColorKey = pickRandomProperty(colorList);
    var newColor = colorList[newColorKey];
    return newColor;
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
    height: calc(100vh - 344px);
  }
  .container-chart.world {
    height: calc(100% - 354px);
  }
  .container-header label {
    margin: 0;
    display: block;
  }
  .container-header {
    padding-bottom: 10px;
  }
  @media (max-width: 1280px) {
    .container-chart.world {
      height: calc(100% - 354px);
    }
  }
</style>

<div class="container-basic container-chart world" bind:this={container_box}>

  <div class="container-header">

    <div class="container-header-contents">

      <h5 class="container-title">Number of infected per million People</h5>
      <label>— 10 worst countrys</label>

    </div>
  </div>
  <div class="container-body">
    <canvas id="myChart" bind:this={canvasElement} />
  </div>
</div>
