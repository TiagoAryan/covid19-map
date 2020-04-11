<script>
  import { createEventDispatcher } from "svelte";
  import { s, flag, getPop } from "misc";
  const dispatch = createEventDispatcher();

  export let data;
  export let country;
  export let name;

  let box_title = "Current Situation";
  let btn_text = "Current";
  var list_mode;
  var show_options = "hidden";

  let res, borders;
  let border = 0,
    active = 0,
    activet = 0,
    active1 = 0,
    active7 = 0,
    active30 = 0,
    deaths = 0,
    deathst = 0,
    deaths1 = 0,
    deaths7 = 0,
    deaths30 = 0,
    confirmed = 0,
    confirmedt = 0,
    confirmed1 = 0,
    confirmed7 = 0,
    confirmed30 = 0,
    recovered = 0,
    recoveredt = 0,
    recovered1 = 0,
    recovered7 = 0,
    recovered30 = 0,
    points = 0;
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
        deathst = d.latest;
        deaths1 =
          deaths - Object.values(d.history)[length - 2] > 0
            ? deaths - Object.values(d.history)[length - 2]
            : 0;
        deaths7 =
          deaths - Object.values(d.history)[length - 8] > 0
            ? deaths - Object.values(d.history)[length - 8]
            : 0;
        deaths30 =
          deaths - Object.values(d.history)[length - 31] > 0
            ? deaths - Object.values(d.history)[length - 31]
            : 0;

        let r = data.recovered.locations.filter(
          e => country === e.country_code
        )[0];
        recovered = r.latest;
        recoveredt = r.latest;
        recovered1 =
          recovered - Object.values(r.history)[length - 2] > 0
            ? recovered - Object.values(r.history)[length - 2]
            : 0;
        recovered7 =
          recovered - Object.values(r.history)[length - 8] > 0
            ? recovered - Object.values(r.history)[length - 8]
            : 0;
        recovered30 =
          recovered - Object.values(r.history)[length - 31] > 0
            ? recovered - Object.values(r.history)[length - 31]
            : 0;

        let c = data.confirmed.locations.filter(
          e => country === e.country_code
        )[0];
        confirmed = c.latest;
        active = c.latest - deaths - recovered;
        confirmedt = confirmed;
        activet = active;
        active1 =
          confirmed - Object.values(c.history)[length - 2] > 0
            ? confirmed - Object.values(c.history)[length - 2]
            : 0;
        active7 =
          confirmed - Object.values(c.history)[length - 8] > 0
            ? confirmed - Object.values(c.history)[length - 8]
            : 0;
        active30 =
          confirmed - Object.values(c.history)[length - 31] > 0
            ? confirmed - Object.values(c.history)[length - 31]
            : 0;
        confirmed1 = active1 + deaths1 + recovered1;
        confirmed7 = active7 + deaths7 + recovered7;
        confirmed30 = active30 + deaths30 + recovered30;

        getBorders();
        getSituation();
      }
    } else {
      deaths = data.latest.deaths;
      recovered = data.latest.recovered;
      confirmed = data.latest.confirmed;
      active = data.latest.confirmed - deaths - recovered;

      deathst = deaths;
      recoveredt = recovered;
      confirmedt = confirmed;
      activet = active;

      let deaths_data = sort_total(deaths, data.deaths.locations);
      let confirmed_data = sort_total(confirmed, data.confirmed.locations);
      let recovered_data = sort_total(recovered, data.recovered.locations);

      deaths1 = deaths - deaths_data[length - 2];
      deaths7 = deaths - deaths_data[length - 8];
      deaths30 = deaths - deaths_data[length - 31];

      recovered1 = recovered - recovered_data[length - 2];
      recovered7 = recovered - recovered_data[length - 8];
      recovered30 = recovered - recovered_data[length - 31];

      active1 = confirmed - confirmed_data[length - 2];
      active7 = confirmed - confirmed_data[length - 8];
      active30 = confirmed - confirmed_data[length - 31];

      confirmed1 = active1 + deaths1 + recovered1;
      confirmed7 = active7 + deaths7 + recovered7;
      confirmed30 = active30 + deaths30 + recovered30;
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

  function getSituation() {
    let fatality = (deaths / confirmed) * 100;

    let length = Object.keys(data.confirmed.locations[0].history).length;

    let pc7 = Object.values(
      data.confirmed.locations.filter(e => country === e.country_code)[0]
        .history
    )[length - 8];
    let pc0 = Object.values(
      data.confirmed.locations.filter(e => country === e.country_code)[0]
        .history
    )[length - 1];

    let inf = ((pc0 / getPop("code", country)) * 100 * 100 * 2) / 7;
    if (inf > 2) inf = 2;

    let gro = ((100 - (pc7 / pc0) * 100) * 4) / 50;
    if (gro > 4) gro = 4;

    let fat = (fatality * 4) / 13;
    if (fat > 4) fat = 4;

    points = 10 - (inf + gro + fat);
  }

  function findCode(items, attribute, value) {
    for (var i = 0; i < items.length; i++) {
      if (items[i][attribute] === value) {
        return items[i].code;
      }
    }
    return null;
  }

  function cchange() {
    dispatch("cchange");
  }

  function toggleOptions() {
    if (show_options == "hidden") {
      show_options = "show";
    } else {
      show_options = "hidden";
    }
  }

  function changeListDisplay(mode) {
    if (mode == "total") {
      list_mode = "total";
      box_title = "Current Situation";
      btn_text = "Current";
      deaths = deathst;
      active = activet;
      confirmed = confirmedt;
      recovered = recoveredt;
    } else if (mode == "today") {
      list_mode = "today";
      box_title = "Today";
      btn_text = "Today";
      active = active1;
      deaths = deaths1;
      confirmed = confirmed1;
      recovered = recovered1;
    } else if (mode == "last7") {
      list_mode = "last7";
      box_title = "Last 7 Days";
      btn_text = "Last 7 Day";
      active = active7;
      deaths = deaths7;
      confirmed = confirmed7;
      recovered = recovered7;
    } else if (mode == "last30") {
      list_mode = "last30";
      box_title = "Last 30 Days";
      btn_text = "Last 30 Days";
      active = active30;
      deaths = deaths30;
      confirmed = confirmed30;
      recovered = recovered30;
    }
    show_options = "hidden";
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
    padding-bottom: 0px;
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
  .col-points {
    float: right;
    margin: 0 8px;
  }
  .col-points .label-big h4 {
    display: inline-block;
    margin: 0;
    font-weight: 500;
  }
  .label-big {
    padding: 2px 10px;
    border-radius: 6px;
    display: inherit;
  }
  .label-yellow {
    color: #ffc831;
    background-color: rgba(255, 200, 49, 0.2);
  }
  .label-red {
    color: #ff4e34;
    background-color: rgba(255, 78, 52, 0.2);
  }
  .label-green {
    color: #40c0a5;
    background-color: rgba(64, 192, 165, 0.2);
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
      {#if country}
        <div class="col-points">
          <div
            class="label-big {points > 7 ? 'label-green' : ''}
            {points >= 3 && points <= 7 ? 'label-yellow' : ''}
            {points < 3 ? 'label-red' : ''}">

            <h4 title="progress of the situation in the last 7 days">
              {parseInt(points)} / 10
            </h4>
          </div>
        </div>
      {/if}
    </div>
  </div>
  <div
    class="container-body"
    style=" width: 100%; padding-bottom: 28px; margin-bottom: 16px;">
    <div class="container-data-details">
      <div class="col-block">
        <i class="dot dot_green" />
        <label>Recovered</label>
        <div class="data">{s(recovered)}</div>
      </div>
      <div class="col-block">
        <i class="dot dot_yellow" />
        <label>Active</label>
        <div class="data">{s(active)}</div>
      </div>
      <div class="col-block">
        <i class="dot dot_red" />
        <label>Deaths</label>
        <div class="data">{s(deaths)}</div>
      </div>
      <div class="col-block-btn">
        <div class="dropdown">
          <div class="trigger" on:click={() => toggleOptions()}>
            <i class="fas fa-filter" style="margin-right: 4px;" />
            {btn_text}
            <i
              class="fas fa-chevron-down"
              style="float: right; margin: 4% 0;" />
          </div>
          <div class="options {show_options}">
            <div class="option" on:click={() => changeListDisplay('total')}>
              <i class="fas fa-battery-full" />
              Current
            </div>
            <div class="option" on:click={() => changeListDisplay('today')}>
              <i class="fas fa-chart-bar" />
              Today
            </div>
            <div class="option" on:click={() => changeListDisplay('last7')}>
              <i class="fas fa-chart-bar" />
              Last 7 Days
            </div>
            <div class="option" on:click={() => changeListDisplay('last30')}>
              <i class="fas fa-chart-bar" />
              Last 30 Days
            </div>
          </div>

        </div>
      </div>
    </div>
    <div class="progress">
      <div
        class="progress-bar"
        role="progressbar"
        style="width: {recovered ? (recovered * 100) / confirmed : 0}%"
        aria-valuenow={recovered ? (recovered * 100) / confirmed : 0}
        aria-valuemin="0"
        aria-valuemax="100">
        {(recovered ? (recovered * 100) / confirmed : 0).toFixed(0)}%
      </div>
      <div
        class="progress-bar bg-warning"
        role="progressbar"
        style="width: {confirmed - deaths - recovered ? ((confirmed - deaths - recovered) * 100) / confirmed : 0}%"
        aria-valuenow={confirmed - deaths - recovered ? ((confirmed - deaths - recovered) * 100) / confirmed : 0}
        aria-valuemin="0"
        aria-valuemax="100">
        {(confirmed - deaths - recovered ? ((confirmed - deaths - recovered) * 100) / confirmed : 0).toFixed(0)}%
      </div>
      <div
        class="progress-bar bg-danger"
        role="progressbar"
        style="width: {deaths ? (deaths * 100) / confirmed : 0}%"
        aria-valuenow={deaths ? (deaths * 100) / confirmed : 0}
        aria-valuemin="0"
        aria-valuemax="100">
        {(deaths ? (deaths * 100) / confirmed : 0).toFixed(0)}%
      </div>
    </div>
    <label class="progress_label">{s(confirmed)} Cases</label>
  </div>
</div>
