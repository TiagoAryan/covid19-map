<script>
  import data from "jhucsse.covid";
  import * as population from "./country-by-population.json";

  export let country;
  export let name;

  let res;
  let pop_total, deaths, confirmed, recovered;

  $: setpop = false;

  $: country, getContent();

  function getContent() {
    if (country) {
      data.getCountry({ cc: country }).then(function(result) {
        res = result;

        res.deaths = Object.values(res.deaths);
        res.confirmed = Object.values(res.confirmed);
        res.recovered = Object.values(res.recovered);

        deaths = res.deaths[res.deaths.length - 1];
        confirmed = res.confirmed[res.confirmed.length - 1];
        recovered = res.recovered[res.recovered.length - 1];
        if (setpop) {
          pop_total = findPop(population.default, "code", country);
        } else {
          pop_total = confirmed;
        }
      });
    } else {
      data.all().then(function(result) {
        res = result;

        deaths = res.latest.deaths;
        confirmed = res.latest.confirmed;
        recovered = res.latest.recovered;
        if (setpop) {
          pop_total = 7772494610;
        } else {
          pop_total = confirmed;
        }
      });
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
  getContent();
</script>

<style>

</style>

{#if !res}
  Loading...
{:else}
<div class="container-basic container-bottom">

  <div class="container-header">
    <div class="container-header-contents">
      <div class="flag">
        <img src="flags/{country ? country : 'world'}.png" alt="flag" />
      </div>
      <h5 class="container-title">{country ? name : 'World'}</h5>
    </div>
  </div>
  <div class="container-body">

    <div class="container-data-details">
      <div class="col-block">
        <i class="dot dot_red" />
        <label>Deaths</label>
        <div class="data">{deaths}</div>
      </div>
      <div class="col-block">
        <i class="dot dot_yellow" />
        <label>Active</label>
        <div class="data">{confirmed - deaths - recovered}</div>
      </div>
      <div class="col-block">
        <i class="dot dot_green" />
        <label>Recovered</label>
        <div class="data">{recovered}</div>
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
          {pop_total.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ')}
          {setpop ? 'Population' : 'Cases'}
        </label>
  </div>
</div>
{/if}
