<script>
  import * as population from "./country-by-population.json";
  import * as country_by_flag from "./country-by-flag.json";
  import { s } from "misc";

  export let data;
  export let country;
  export let name;
  let pop_total, deaths, confirmed, recovered;

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
      setpop = false;
      pop_total = confirmed;
    } else {
      setpop = true;
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

</style>

<div class="container-basic container-bottom container-total">

  <div class="container-header">
    <div class="container-header-contents">
      <div class="flag">
        <img
          src={country_by_flag.default.filter(e => name === e.country)[0].flag_base64}
          alt="flag" />
      </div>
      <h5 class="container-title">{name}</h5>
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
