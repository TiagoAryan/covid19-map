<script>
  import { getPop } from "misc";

  export let data;
  export let country;

  let days,
    fatality = 0,
    pop_total,
    points,
    critical,
    tests,
    critical_per;

  $: country, getContent();

  function getContent() {
    if (data.confirmed.locations.filter(e => country === e.country_code)[0]) {
      if (country == "CN") days = 66;
      else if (country == "TH" || country == "NP") days = 9;
      else if (country == "JP") days = 6;
      else if (country == "US") days = 3;
      else if (country == "KR") days = 2;
      else days = 0;

      let d = data.confirmed.locations.filter(
        e => country === e.country_code
      )[0].history;
      Object.keys(d).forEach(function(key) {
        if (d[key] != 0) days++;
      });

      let deaths = data.deaths.locations.filter(
        e => country === e.country_code
      )[0].latest;
      let confirmed = data.confirmed.locations.filter(
        e => country === e.country_code
      )[0].latest;
      let recovered = data.recovered.locations.filter(
        e => country === e.country_code
      )[0].latest;
      critical = data.confirmed.locations.filter(
        e => country === e.country_code
      )[0].critical;
      tests = data.confirmed.locations.filter(
        e => country === e.country_code
      )[0].tests;

      critical_per = (critical / (confirmed - deaths - recovered)) * 100;

      fatality = (deaths / confirmed) * 100;
      pop_total = getPop("code", country);
    }
  }
</script>

<style>
  .container-stats {
    position: relative;
    display: inline-block;
    margin: 0;
    margin-bottom: 12px;
    height: 110px;
    width: 100%;
    left: 0;
    vertical-align: top;
  }
  .container-data-details {
    text-align: center;
    padding-top: 8px;
  }
  .col-block {
    width: 24%;
    text-align: left;
    padding: 0px 4%;
    vertical-align: top;
  }
  h4 {
    margin: 0px;
  }
  .label-big h4 {
    display: inline-block;
    margin: 0;
    font-weight: 600;
  }
  .label-big {
    padding: 6px 12px;
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
  .col-block label {
    display: block;
    margin-bottom: 4px;
    line-height: 0.8rem;
  }

  @media (max-width: 768px) {
    .col-block {
      width: 18%;
      padding: 0px 0%;
    }
  }
  @media (max-width: 480px) {
    .col-block {
      width: 32%;
      padding: 0px 0%;
    }
    .col-block:nth-of-type(1) {
      width: 100%;
      margin-bottom: 12px;
    }
    .container-stats {
      height: auto;
    }
  }
</style>

<div class="container-basic container-stats">

  <div class="container-body">

    <div class="container-data-details">
      <div class="col-block">
        <label>Days Infected</label>
        <h4>{days} Days</h4>
      </div>
      <div class="col-block">
        <label>Tests</label>
        <div
          class="label-big {(tests / pop_total) * 1000 > 10 ? 'label-green' : ''}
          {(tests / pop_total) * 1000 > 5 && (tests / pop_total) * 1000 <= 10 ? 'label-yellow' : ''}
          {(tests / pop_total) * 1000 < 5 ? 'label-red' : ''}
          ">
          <h4>
            {parseInt(tests / 1000000) > 0 ? parseInt(tests / 1000000) + 'M' : ''}
            {parseInt(tests / 1000) > 0 && parseInt(tests / 1000000) <= 0 ? parseInt(tests / 1000) + 'k' : ''}
            {parseInt(tests / 1000) <= 0 ? tests : ''}
          </h4>

        </div>
      </div>
      <div class="col-block">
        <label>In Critical State</label>
        <div
          class="label-big {critical_per > 7 ? 'label-red' : ''}
          {critical_per > 3.5 && critical_per <= 7 ? 'label-yellow' : ''}
          {critical_per < 5 ? 'label-green' : ''}
          ">
          <h4>{critical}</h4>
        </div>
      </div>
      <div class="col-block">
        <label>Fatality</label>
        <div
          class="label-big {fatality > 10 ? 'label-red' : ''}
          {fatality > 5 && fatality <= 10 ? 'label-yellow' : ''}
          {fatality < 5 ? 'label-green' : ''}
          ">

          <h4>{fatality.toFixed(1)}%</h4>
        </div>
      </div>
    </div>
  </div>
</div>
