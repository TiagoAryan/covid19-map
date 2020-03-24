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
      console.log(country);
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
          pop_total = deaths + confirmed + recovered;
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
          pop_total = deaths + confirmed + recovered;
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
      pop_total = deaths + confirmed + recovered;
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

  <div class="flag">
    <img src="flags/{country ? country : 'world'}.png" alt="flag" />
  </div>
  <h5 class="container-title">{country ? name : 'World'}</h5>
  <div class="container-data-details">
    <div class="col-block">
      <i class="dot dot_red" />
      <label>Deaths</label>
      <div class="data">{deaths}</div>
    </div>
    <div class="col-block">
      <i class="dot dot_yellow" />
      <label>Confirmed</label>
      <div class="data">{confirmed}</div>
    </div>
    <div class="col-block">
      <i class="dot dot_green" />
      <label>Recovered</label>
      <div class="data">{recovered}</div>
    </div>
     <div class="col-block-btn">
        
        <div class="button adj-right" on:click={() => change()}>
          <i class="fas fa-user-friends"></i>
          All
        </div>
        <div class="button secondary adj-left" on:click={() => change()}>    
          <i class="fas fa-procedures"></i>
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
      style="width: {confirmed ? (confirmed * 100) / pop_total : 0}%"
      aria-valuenow={confirmed ? (confirmed * 100) / pop_total : 0}
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
    {setpop ? 'Population' : 'Infected'}
  </label>
{/if}
