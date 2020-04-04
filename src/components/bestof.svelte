<script>
  import { onMount } from "svelte";
  import * as population from "./country-by-population.json";
  import * as country_by_flag from "./country-by-flag.json";
  import { s } from "misc";

  export let data;
  export let show;

  let all, length;
  let all_sorted = [],
    all_order = [];

  $: show && run();

  function run() {
    length = Object.keys(data.confirmed.locations[0].history).length;

    all_sorted = [];
    all_order = [];
    if (show == "deaths") all = data.deaths.locations;
    else if (show == "confirmed") all = data.confirmed.locations;
    else if (show == "recovered") all = data.recovered.locations;

    all_sorted = all.sort(function(a, b) {
      return b.latest - a.latest;
    });
  }
</script>

<style>
  .container-bestof {
    position: fixed;
    left: 84px;

    width: 380px;
    height: 460px;

    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;

    pointer-events: all;
  }
  .container-bestof.hidden {
    height: 0px;
    pointer-events: none;
    opacity: 0;
  }
  .container-bestof .container-body {
    max-height: 420px;
    overflow: scroll;
  }

  #bestof_confirmed {
    top: 100px;
  }

  #bestof_recovered {
    top: 172px;
  }
  #bestof_deaths {
    top: 244px;
  }
</style>

<div
  id="bestof_{show}"
  class="container-basic container-bestof {show ? '' : 'hidden'}">
  <div class="container-header">
    <div class="container-header-contents">

      <h5 class="container-title">Most {show}</h5>
    </div>
  </div>
  <div class="container-body">
    <div class="container-list">
      {#each all_sorted.slice(0, 10) as item, i}
        <li>
          <label>
            {#if Object.values(all_sorted[i].history)[length - 2] < Object.values(all_sorted[i + 1].history)[length - 2]}
              <i class="fas fa-sort-up" />
            {:else if i > 0 && Object.values(all_sorted[i].history)[length - 2] > Object.values(all_sorted[i - 1].history)[length - 2]}
              <i class="fas fa-sort-down" />
            {:else}#{/if}
            {i + 1}
          </label>
          <div class="flag">
            <img
              src={country_by_flag.default.filter(e => item.country === e.country)[0].flag_base64}
              alt="flag" />
          </div>
          <p class="list-name">{item.country}</p>
          <p class="list-count">
            <span class="badge badge-light">
              + {s(item.latest - Object.values(item.history)[length - 2])}
            </span>
            {s(item.latest)}
          </p>
        </li>
      {/each}
    </div>
  </div>
</div>
