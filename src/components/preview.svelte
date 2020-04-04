<script>
  import * as population from "./country-by-population.json";
  import { s, flag } from "misc";

  export let data;
  export let country;
  export let name;

  let res, borders;
  let border = 0,
    pop_total = 0,
    deaths,
    confirmed,
    recovered;
  let cland, csea, cair;

  $: setpop = false;

  $: country, getContent();

  function getContent() {
    if (country) {
      deaths = data.deaths.locations.filter(e => country === e.country_code)[0]
        .latest;
      confirmed = data.confirmed.locations.filter(
        e => country === e.country_code
      )[0].latest;
      recovered = data.recovered.locations.filter(
        e => country === e.country_code
      )[0].latest;

      if (setpop) {
        pop_total = findPop(population.default, "code", country);
      } else {
        pop_total = confirmed;
      }
      getBorders();
    } else {
      deaths = data.latest.deaths;
      confirmed = data.latest.confirmed;
      recovered = data.latest.recovered;
      if (setpop) {
        pop_total = 7772494610;
      } else {
        pop_total = confirmed;
      }
    }
  }

  async function getBorders() {
    if (!borders) {
      const response = await fetch("./borders.json");
      borders = await response.json();
    }

    if (borders.filter(e => country === e.code)[0]) {
      border = borders.filter(e => country === e.code)[0].transit;

      if (border.land == 1) cland = "#40c0a5";
      else if (border.land == 2) cland = "#ff4e34";
      else if (border.land == 3) cland = "#ffc831";

      if (border.sea == 1) csea = "#40c0a5";
      else if (border.sea == 2) csea = "#ff4e34";
      else if (border.sea == 3) csea = "#ffc831";

      if (border.air == 1) cair = "#40c0a5";
      else if (border.air == 2) cair = "#ff4e34";
      else if (border.air == 3) cair = "#ffc831";
    } else border = 0;
  }

  function findCode(items, attribute, value) {
    for (var i = 0; i < items.length; i++) {
      if (items[i][attribute] === value) {
        return items[i].code;
      }
    }
    return null;
  }

  function findPop(items, attribute, value) {
    for (var i = 0; i < items.length; i++) {
      if (items[i][attribute] === value) {
        return items[i].z * 1000;
      }
    }
    return null;
  }

  function change() {
    if (setpop) {
      setpop = !setpop;
      pop_total = confirmed;
    } else {
      setpop = !setpop;
      if (country) pop_total = findPop(population.default, "code", country);
      else pop_total = 7772494610;
    }
  }
  function showContainers() {
    var el = document.querySelector(".container_details_box");
    el.classList.toggle("hidden");
    /*
    var el2 = document.querySelector(".container-countries");
    el2.classList.toggle("hidden");
    */
  }
</script>

<style>
  .container-total {
    position: relative;
    display: inline-block;
    margin: 0;
    margin-bottom: 12px;
    height: 190px;
    width: 100%;
    left: 0;
    vertical-align: top;
  }
  .borders {
    display: inline;
  }
  .borders i {
    font-weight: 900;
    border: 1px solid white;
    font-size: 9pt;
    padding: 2px;
    border-radius: 4px;
  }
</style>

<div class="container-basic container-bottom container-total">

  <div class="container-header">
    <div class="container-header-contents">
      <div class="flag">
        <img src={flag(name)} alt="flag" />
      </div>

      <h5 class="container-title">{name}</h5>
      <div class="borders">
        {#if border}
          {#if border.land}
            <i class="fas fa-car" style="color:{cland}" />
          {/if}
          {#if border.sea}
            <i class="fas fa-ship" style="color:{csea}" />
          {/if}
          {#if border.air}
            <i class="fas fa-plane" style="color:{cair}" />
          {/if}
        {/if}
      </div>
      <div style="float:right" class="button" on:click={() => showContainers()}>
        <i class="fas fa-user-friends" />
        Details
      </div>
    </div>
  </div>
  <div class="container-body">

    <div class="container-data-details">
      <div class="col-block">
        <i class="dot dot_red" />
        <label>Deaths</label>
        <div class="data">{s(deaths)}</div>
      </div>
      <div class="col-block">
        <i class="dot dot_yellow" />
        <label>Active</label>
        <div class="data">{s(confirmed - deaths - recovered)}</div>
      </div>
      <div class="col-block">
        <i class="dot dot_green" />
        <label>Recovered</label>
        <div class="data">{s(recovered)}</div>
      </div>
      <div class="col-block-btn">
        <div class="button" on:click={() => change()}>
          <i class="fas fa-user-friends" />
          All
        </div>
      </div>
    </div>
    <div class="progress">
      <div
        class="progress-bar bg-danger"
        role="progressbar"
        style="width: {deaths ? (deaths * 100) / pop_total : 0}%"
        aria-valuenow={deaths ? (deaths * 100) / pop_total : 0}
        aria-valuemin="0"
        aria-valuemax="100" />
      <div
        class="progress-bar bg-warning"
        role="progressbar"
        style="width: {confirmed - deaths - recovered ? ((confirmed - deaths - recovered) * 100) / pop_total : 0}%"
        aria-valuenow={confirmed - deaths - recovered ? ((confirmed - deaths - recovered) * 100) / pop_total : 0}
        aria-valuemin="0"
        aria-valuemax="100" />
      <div
        class="progress-bar"
        role="progressbar"
        style="width: {recovered ? (recovered * 100) / pop_total : 0}%"
        aria-valuenow={recovered ? (recovered * 100) / pop_total : 0}
        aria-valuemin="0"
        aria-valuemax="100" />
    </div>
    <label class="progress_label">
      {s(pop_total)} {setpop ? 'Population' : 'Cases'}
    </label>
  </div>
</div>
