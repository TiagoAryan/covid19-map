<script>
  import { createEventDispatcher } from "svelte";
  import { s, getPop, flag } from "misc";
  const dispatch = createEventDispatcher();

  export let data;
  export let country;
  export let name;

  let res, borders;
  let border = 0,
    pop_total = 0,
    deaths = 0,
    deaths1 = 0,
    deaths7 = 0,
    deaths30 = 0,
    confirmed = 0,
    confirmed1 = 0,
    confirmed7 = 0,
    confirmed30 = 0,
    recovered = 0,
    recovered1 = 0,
    recovered7 = 0,
    recovered30 = 0;
  let cland, csea, cair;

  $: setpop = false;

  $: country, getContent();

  function getContent() {
    if (country) {
      let length = Object.keys(data.confirmed.locations[0].history).length;
      if (data.deaths.locations.filter(e => country === e.country_code)[0]) {
        let d = data.deaths.locations.filter(
          e => country === e.country_code
        )[0];
        deaths = d.latest;
        deaths1 = deaths - Object.values(d.history)[length - 2];
        deaths7 = deaths - Object.values(d.history)[length - 8];
        deaths30 = deaths - Object.values(d.history)[length - 31];

        let c = data.confirmed.locations.filter(
          e => country === e.country_code
        )[0];
        confirmed = c.latest;
        confirmed1 = confirmed - Object.values(c.history)[length - 2];
        confirmed7 = confirmed - Object.values(c.history)[length - 8];
        confirmed30 = confirmed - Object.values(c.history)[length - 31];
        let r = data.recovered.locations.filter(
          e => country === e.country_code
        )[0];
        recovered = r.latest;
        recovered1 = recovered - Object.values(r.history)[length - 2];
        recovered7 = recovered - Object.values(r.history)[length - 8];
        recovered30 = recovered - Object.values(r.history)[length - 31];

        if (setpop) {
          pop_total = getPop("code", country);
        } else {
          pop_total = confirmed;
        }
        getBorders();
      }
    } else {
      deaths = data.latest.deaths;
      confirmed = data.latest.confirmed;
      recovered = data.latest.recovered;

      let deaths_data = sort_total(deaths, data.deaths.locations);
      let confirmed_data = sort_total(confirmed, data.confirmed.locations);
      let recovered_data = sort_total(recovered, data.recovered.locations);

      deaths1 = deaths - deaths_data[length - 2];
      deaths7 = deaths - deaths_data[length - 8];
      deaths30 = deaths - deaths_data[length - 31];

      confirmed1 = confirmed - confirmed_data[length - 2];
      confirmed7 = confirmed - confirmed_data[length - 8];
      confirmed30 = confirmed - confirmed_data[length - 31];

      recovered1 = recovered - recovered_data[length - 2];
      recovered7 = recovered - recovered_data[length - 8];
      recovered30 = recovered - recovered_data[length - 31];

      if (setpop) {
        pop_total = 7772494610;
      } else {
        pop_total = confirmed;
      }
    }
  }

  function sort_total(last, all) {
    var dates = Object.keys(all[0].history).sort(function(a, b) {
      return new Date(a) - new Date(b);
    });
    var total = [];
    for (var d of dates) {
      var tot = 0;
      for (var item of all)
        if (item.history[d] !== undefined) tot += item.history[d];

      total.push(tot);
    }

    total[total.length - 1] = last;
    return total;
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

  function change() {
    if (setpop) {
      setpop = !setpop;
      pop_total = confirmed;
    } else {
      setpop = !setpop;
      if (country) pop_total = getPop("code", country);
      else pop_total = 7772494610;
    }
  }

  function cchange() {
    dispatch("cchange");
  }
</script>

<style>
  .container-total {
    position: relative;
    display: inline-block;
    margin: 0;
    margin-bottom: 12px;
    width: 100%;
    left: 0;
    vertical-align: top;
  }
  .borders {
    display: inline;
  }
  .borders i {
    font-weight: 900;
    font-size: 9pt;
    padding: 2px;
    border-radius: 4px;
    padding: 4px;
    border: 1px solid white;
  }
  .flag img {
    height: 28px;
    width: 46px;
  }
  .button_close {
    float: right;
  }
  .container-title {
    margin-right: 12px;
  }
  @media (max-width: 1280px) {
    .container-total {
      height: auto;
    }
  }
  @media (max-width: 768px) {
    .container-total {
      height: auto;
    }
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
            <i
              class="fas fa-car"
              style="border-color: {cland}; color:{cland}" />
          {/if}
          {#if border.sea}
            <i class="fas fa-ship" style="border-color: {csea}; color:{csea}" />
          {/if}
          {#if border.air}
            <i
              class="fas fa-plane"
              style="border-color: {cair}; color:{cair}" />
          {/if}
        {/if}
      </div>
      <div class="button secondary button_close" on:click={cchange}>
        <i class="fas fa-times" />
        Close
      </div>
    </div>
  </div>
  <div
    class="container-body"
    style="border-bottom: 1px solid rgba(151, 151, 151, 0.3); width: 100%;
    padding-bottom: 28px; margin-bottom: 16px;">
    <div class="container-data-details">
      <div class="col-block">
        <i class="dot dot_green" />
        <label>Recovered</label>
        <div class="data">{s(recovered)}</div>
      </div>
      <div class="col-block">
        <i class="dot dot_yellow" />
        <label>Active</label>
        <div class="data">{s(confirmed - deaths - recovered)}</div>
      </div>
      <div class="col-block">
        <i class="dot dot_red" />
        <label>Deaths</label>
        <div class="data">{s(deaths)}</div>
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
        class="progress-bar"
        role="progressbar"
        style="width: {recovered ? (recovered * 100) / pop_total : 0}%"
        aria-valuenow={recovered ? (recovered * 100) / pop_total : 0}
        aria-valuemin="0"
        aria-valuemax="100">
        {(recovered ? (recovered * 100) / pop_total : 0).toFixed(0)}%
      </div>
      <div
        class="progress-bar bg-warning"
        role="progressbar"
        style="width: {confirmed - deaths - recovered ? ((confirmed - deaths - recovered) * 100) / pop_total : 0}%"
        aria-valuenow={confirmed - deaths - recovered ? ((confirmed - deaths - recovered) * 100) / pop_total : 0}
        aria-valuemin="0"
        aria-valuemax="100">
        {(confirmed - deaths - recovered ? ((confirmed - deaths - recovered) * 100) / pop_total : 0).toFixed(0)}%
      </div>
      <div
        class="progress-bar bg-danger"
        role="progressbar"
        style="width: {deaths ? (deaths * 100) / pop_total : 0}%"
        aria-valuenow={deaths ? (deaths * 100) / pop_total : 0}
        aria-valuemin="0"
        aria-valuemax="100">
        {(deaths ? (deaths * 100) / pop_total : 0).toFixed(0)}%
      </div>
    </div>
    <label class="progress_label">
      {s(pop_total)} {setpop ? 'Population' : 'Cases'}
    </label>
  </div>

  <div class="container-body">
    <div class="container-data-details">
      <div class="table-responsive-lg">
        <table class="table table-sm table-dark">
          <thead>
            <tr>
              <th scope="col" />
              <th scope="col">Cases</th>
              <th scope="col">Recovered</th>
              <th scope="col">Deaths</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <th scope="row">Today</th>
              <td>{s(confirmed1)}</td>
              <td>{s(recovered1)}</td>
              <td>{s(deaths1)}</td>
            </tr>
            <tr>
              <th scope="row">Last 7 Days</th>
              <td>{s(confirmed7)}</td>
              <td>{s(recovered7)}</td>
              <td>{s(deaths7)}</td>
            </tr>
            <tr>
              <th scope="row">Last 30 Days</th>
              <td>{s(confirmed30)}</td>
              <td>{s(recovered30)}</td>
              <td>{s(deaths30)}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>
