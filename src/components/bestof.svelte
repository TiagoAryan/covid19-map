<script>
  import data from "jhucsse.covid";
  import * as population from "./country-by-population.json";
	import { onMount } from 'svelte';

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

  getContent();
  onMount(() => {
    initChart();
  });


//---------------
// CHART
//---------------
function initChart(){
  console.log("LOADED")
  var ctx = document.getElementById('myChart').getContext('2d');

  var myChart = new Chart(ctx, {
    type: 'bar',
    data: {
        labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
        datasets: [{
            label: '# of Votes',
            data: [12, 19, 3, 5, 2, 3],
            backgroundColor: [
                'rgba(255, 99, 132, 0.2)',
                'rgba(54, 162, 235, 0.2)',
                'rgba(255, 206, 86, 0.2)',
                'rgba(75, 192, 192, 0.2)',
                'rgba(153, 102, 255, 0.2)',
                'rgba(255, 159, 64, 0.2)'
            ],
            borderColor: [
                'rgba(255, 99, 132, 1)',
                'rgba(54, 162, 235, 1)',
                'rgba(255, 206, 86, 1)',
                'rgba(75, 192, 192, 1)',
                'rgba(153, 102, 255, 1)',
                'rgba(255, 159, 64, 1)'
            ],
            borderWidth: 1
        }]
    },
    options: {
        scales: {
            yAxes: [{
                ticks: {
                    beginAtZero: true
                }
            }]
        }
    }
});
}
</script>

<style>
  .container-bestof {
    position: absolute;
    left: 84px;
    top:100px;
    width: 380px;
  }
  .container-bestof-fit{
    margin: 0 auto;
  }
  .container-list{
    display: block;
    position: relative;
  }
  .container-list li{
    display: block;
    padding: 6px 0px;
  }
  .container-chart{
    width: 800px;
    height: 300px;
  }

</style>

{#if !res}
  Loading...
{:else}
<div class="container-basic container-bestof">

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
              <img src="flags/{item.country_code ? item.country_code : 'world'}.png" alt="flag" />
            </div>
            <p class="list-name">{item.country} </p>
            <p class="list-count">{item.latest} </p> 
          </li>
        {/each}
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
      <canvas id="myChart"></canvas>
    </div>
</div>
