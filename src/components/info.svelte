<script>
  import { createEventDispatcher } from "svelte";
  import { s, getPop, flag } from "misc";
  const dispatch = createEventDispatcher();

  export let data;
  export let country;
  export let name;
  export let show_details;

  let pop_total, deaths, confirmed, recovered;

  $: show_details;

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
          pop_total = getPop("code", country);
        } else {
          pop_total = confirmed;
        }
      } else {
        deaths = 0;
        confirmed = 0;
        recovered = 0;
        if (setpop) {
          pop_total = getPop("code", country);
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

  function fitMap() {
    dispatch("fitMap", {
      country: name
    });
  }
</script>

<style>
  .container-total {
    bottom: -200px;
  }
  .container-total.show {
    bottom: 0px;
  }

  .body-healthy {
    color: white;
    opacity: 0.4;
    padding: 20px 0px;
    text-align: center;
  }
  .body-healthy h5 {
    display: inline-block;
    font-weight: 300;
  }
  .body-healthy i {
    margin-right: 12px;
  }
  .flag img {
    height: 28px;
    width: 46px;
  }
</style>

<div
  class="container-basic container-bottom container-total {show_details ? 'hidden' : 'show'}">

  <div class="container-header">
    <div class="container-header-contents">
      <div class="flag">
        <img src={flag(name)} alt="flag" />
      </div>
      <h5 class="container-title">{name}</h5>
      <div style="float:right" class="button" on:click={fitMap}>
        <i class="fas fa-user-friends" />
        Details
      </div>
    </div>
  </div>
  {#if confirmed}
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
  {:else}
    <div class="container-body body-healthy">
      <i class="fas fa-heartbeat" />
      <h5>This country has no cases of Covid-19</h5>
      <i class="fas fa-heartbeat" />
    </div>
  {/if}
</div>
