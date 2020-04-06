<script>
  import * as population from "./country-by-population.json";
  import { s, flag } from "misc";

  export let data;
  export let country;
  export let name;
  let pop_total, deaths, confirmed, recovered;

  let show_container = "show";
  $: setpop = false;

  $: country, getContent();

  function getContent() {
    if (country) {
      if (data.deaths.locations.filter(e => country === e.country_code)[0]) {
        deaths = data.deaths.locations.filter(
          e => country === e.country_code
        )[0].latest;
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
        deaths = 0;
        confirmed = 0;
        recovered = 0;
        if (setpop) {
          pop_total = findPop(population.default, "code", country);
        } else {
          pop_total = 0;
        }
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
    if (!show_container) show_container = "show";
    // else show_container = "";
  }
</script>

<style>
  .container-total {
    bottom: -200px;
  }
  .container-total.show {
    bottom: 0px;
  }
  .body-infected{
    display: block;
  }
  .body-healthy{
    display: none;
  }
  .healthy .body-infected{
    display: none;
  }
  .healthy .body-healthy{
    display: block;
  }
  .body-healthy{
    color:white;
    opacity: 0.4;
    padding:20px 0px;
    text-align: center;
  }
  .body-healthy h5{
    display: inline-block;
    font-weight: 300;
  }
  .body-healthy i{
    margin-right: 12px;
  }
</style>

<div class="container-basic container-bottom container-total {show_container} {confirmed===0 ? "healthy" : ""}">

  <div class="container-header">
    <div class="container-header-contents">
      <div class="flag">
        <img src={flag(name)} alt="flag" />
      </div>
      <h5 class="container-title">{name}</h5>
      {#if confirmed}
        <div
          style="float:right"
          class="button"
          on:click={() => showContainers()}>
          <i class="fas fa-user-friends" />
          Details
        </div>
      {/if}
    </div>
  </div>
  <div class="container-body body-infected">

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
  <div class="container-body body-healthy">
      <i class="fas fa-heartbeat"></i>
      <h5>This country has no cases of Covid-19</h5>
  </div>
</div>
