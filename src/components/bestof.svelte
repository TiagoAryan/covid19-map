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
</script>

<style>
  .container-bestof {
    position: fixed;
    left: 84px;

    width: 380px;
    height: 590px;

    opacity: 1;
    -webkit-transition-duration: .4s;
    -moz-transition-duration: .4s;
    -o-transition-duration: .4s;
    transition-duration: .4s;

    pointer-events: all;

  }
  .container-bestof.hidden {
    height: 0px;
    pointer-events: none;
    opacity: 0;

  }
  .container-bestof .container-body{
    height: 420px;
    overflow: scroll;
  }

  #bestof_confirmed{
    top: 100px; 
}
  
  #bestof_recovered{
    top: 172px; 
  }
  #bestof_deaths{
      top: 244px; 
  }
</style>

{#if !res}
  Loading...
{:else}
  <div id="bestof_{type}" class="container-basic container-bestof hidden">
    <div class="container-header">
      <div class="container-header-contents">

        <h5 class="container-title">Most {type}</h5>
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



