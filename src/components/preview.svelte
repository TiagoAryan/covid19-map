<script>
  import { createEventDispatcher } from "svelte";
  import { s, flag, getPop } from "misc";
  const dispatch = createEventDispatcher();

  export let data;
  export let country;
  export let name;
  export let show_details;

  let box_title = "Current Situation";
  let btn_text = "Current";
  let infected = "Active";
  var list_mode;
  var show_options = "hidden";

  let res, borders;
  let border = 0,
    active = 0,
    deaths = 0,
    confirmed = 0,
    recovered = 0,
    points = 0;
  let cland, csea, cair;

  $: country, list_mode, getContent();

  function getContent() {
    if (country) {
      let length = Object.keys(data.confirmed.locations[0].history).length;
      if (data.deaths.locations.filter(e => country === e.country_code)[0]) {
        let d = data.deaths.locations.filter(
          e => country === e.country_code
        )[0];
        let r = data.recovered.locations.filter(
          e => country === e.country_code
        )[0];
        let c = data.confirmed.locations.filter(
          e => country === e.country_code
        )[0];

        if (list_mode == "today") {
          deaths =
            d.latest - Object.values(d.history)[length - 2] > 0
              ? d.latest - Object.values(d.history)[length - 2]
              : 0;
          recovered =
            r.latest - Object.values(r.history)[length - 2] > 0
              ? r.latest - Object.values(r.history)[length - 2]
              : 0;
          active =
            c.latest - Object.values(c.history)[length - 2] > 0
              ? c.latest - Object.values(c.history)[length - 2]
              : 0;

          confirmed = active + deaths + recovered;
        } else if (list_mode == "last7") {
          deaths =
            d.latest - Object.values(d.history)[length - 8] > 0
              ? d.latest - Object.values(d.history)[length - 8]
              : 0;
          recovered =
            r.latest - Object.values(r.history)[length - 8] > 0
              ? r.latest - Object.values(r.history)[length - 8]
              : 0;
          active =
            c.latest - Object.values(c.history)[length - 8] > 0
              ? c.latest - Object.values(c.history)[length - 8]
              : 0;
          confirmed = active + deaths + recovered;
        } else if (list_mode == "last30") {
          deaths =
            d.latest - Object.values(d.history)[length - 31] > 0
              ? d.latest - Object.values(d.history)[length - 31]
              : 0;
          recovered =
            r.latest - Object.values(r.history)[length - 31] > 0
              ? r.latest - Object.values(r.history)[length - 31]
              : 0;
          active =
            c.latest - Object.values(c.history)[length - 31] > 0
              ? c.latest - Object.values(c.history)[length - 31]
              : 0;
          confirmed = active + deaths + recovered;
        } else {
          deaths = d.latest;
          recovered = r.latest;
          confirmed = c.latest;
          active = confirmed - deaths - recovered;
        }

        getBorders();
        getSituation(d.latest, c.latest);
      } else {
        if (show_details) cchange();
      }
    } else {
      border = 0;
      let deaths_data = sort_total(deaths, data.deaths.locations);
      let confirmed_data = sort_total(confirmed, data.confirmed.locations);
      let recovered_data = sort_total(recovered, data.recovered.locations);

      if (list_mode == "today") {
        deaths = data.latest.deaths - deaths_data[length - 2];
        recovered = data.latest.recovered - recovered_data[length - 2];
        active = data.latest.confirmed - confirmed_data[length - 2];
        confirmed = active + deaths + recovered;
      } else if (list_mode == "last7") {
        deaths = data.latest.deaths - deaths_data[length - 8];
        recovered = data.latest.recovered - recovered_data[length - 8];
        active = data.latest.confirmed - confirmed_data[length - 8];
        confirmed = active + deaths + recovered;
      } else if (list_mode == "last30") {
        deaths = data.latest.deaths - deaths_data[length - 31];
        recovered = data.latest.recovered - recovered_data[length - 31];
        active = data.latest.confirmed - confirmed_data[length - 13];
        confirmed = active + deaths + recovered;
      } else {
        deaths = data.latest.deaths;
        recovered = data.latest.recovered;
        confirmed = data.latest.confirmed;
        active = confirmed - deaths - recovered;
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

  function getSituation(d, c) {
    let fatality = (d / c) * 100;

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
      infected = "Active";
    } else if (mode == "today") {
      list_mode = "today";
      box_title = "Today";
      btn_text = "Today";
      infected = "Infected";
    } else if (mode == "last7") {
      list_mode = "last7";
      box_title = "Last 7 Days";
      btn_text = "Last 7 Day";
      infected = "Infected";
    } else if (mode == "last30") {
      list_mode = "last30";
      box_title = "Last 30 Days";
      btn_text = "Last 30 Days";
      infected = "Infected";
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
  .col-points {
    float: right;
    margin: 0 8px;
  }

  .label-big {
    font-weight: 900;
    font-size: 9pt;
    padding: 2px;
    border-radius: 4px;
    display: inline-block;
    padding: 1px 6px;
    height: 22px;
    display: inline-block;
  }
  .label-big i {
    margin-right: 4px;
    border: 0px;
    padding: 0px;
  }
  .label-yellow {
    color: #ffc831;
    border: 1px solid #ffc831;
  }
  .label-red {
    color: #ff4e34;
    border: 1px solid #ff4e34;
  }
  .label-green {
    color: #40c0a5;
    border: 1px solid #40c0a5;
  }

  .fa-chevron-down {
    float: right;
    margin: 4% 0;
    margin-right: 4px;
  }
  @media (max-width: 1280px) {
    .container-total {
      height: auto;
    }
    .fa-chevron-down {
      float: right;
      margin: 4% 0;
      margin-right: 0px;
    }
  }
  @media (max-width: 768px) {
    .container-total {
      height: auto;
    }
    .fa-chevron-down {
      margin: 5px 2px;
    }
  }
  @media (max-width: 480px) {
    .button_close {
      position: absolute;
      right: 12px;
      top: 12px;
    }
    .borders {
      margin-left: 60px;
      display: block;
    }
    .fa-chevron-down {
      margin: 5px 0px;
    }
    .container-total {
      margin-bottom: 3px;
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
        {#if country}
          <div
            title="Progress of the situation in the last 7 days"
            class="label-big {points > 7 ? 'label-green' : ''}
            {points > 3 && points <= 7 ? 'label-yellow' : ''}
            {points <= 3 ? 'label-red' : ''}">

            <i
              class="far {points > 7 ? 'fa-smile' : ''}
              {points > 3 && points <= 7 ? 'fa-meh' : ''}
              {points <= 3 ? 'fa-frown' : ''}" />
            {parseInt(points)} / 10
          </div>
        {/if}
      </div>
      <div class="button secondary button_close" on:click={cchange}>
        <i class="fas fa-times" />
        Close
      </div>

    </div>
  </div>

  {#if active}
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
          <label>{infected}</label>
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
              <i class="fas fa-filter" />
              <p>{btn_text}</p>
              <i class="fas fa-chevron-down" />
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
  {:else}
    <div class="container-body body-healthy">
      <i class="fas fa-heartbeat" />
      <h5>This country has no cases of Covid-19</h5>
      <i class="fas fa-heartbeat" />
    </div>
  {/if}
</div>
