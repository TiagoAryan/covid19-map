<script>
  import { getPop } from "misc";

  export let data;
  export let country;

  let days, fatality, pop_total, points, critical, critical_per;

  $: country, getContent();

  function getContent() {
    if (data.confirmed.locations.filter(e => country === e.country_code)[0]) {
      console.log(data);
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

      critical_per=critical /(confirmed-deaths-recovered)*100;
      console.log(critical);
      console.log(critical_per);


      fatality = (deaths / confirmed) * 100;

      let length = Object.keys(data.confirmed.locations[0].history).length;

      let pc7 = Object.values(
        data.confirmed.locations.filter(e => country === e.country_code)[0]
          .history
      )[length - 8];
      let pc0 = Object.values(
        data.confirmed.locations.filter(e => country === e.country_code)[0]
          .history
      )[length - 1];

      pop_total = getPop("code", country);

      let inf = ((pc0 / pop_total) * 100 * 100 * 2) / 7;
      if (inf > 2) inf = 2;

      let gro = ((100 - (pc7 / pc0) * 100) * 4) / 50;
      if (gro > 4) gro = 4;

      let fat = (fatality * 4) / 13;
      if (fat > 4) fat = 4;

      points = 10 - (inf + gro + fat);
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
  .col-block {
    width: 24%;
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
  }
</style>

<div class="container-basic container-stats">

  <div class="container-body">

    <div class="container-data-details">
      <div class="col-block">
        <label>Days since first infected</label>
        <h4>{days} Days</h4>
      </div>
      <div class="col-block">
        <label>In Critical State</label>
        <div class="label-big {critical_per>7 ? "label-red" : ""} {critical_per>3.5 && critical_per<=7 ? "label-yellow" : ""} {critical_per<5 ? "label-green" : ""} ">
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
      <div class="col-block">
        <label>Current Situation</label>
        <div
          class="label-big {points > 7 ? 'label-green' : ''}
          {points >= 3 && points <= 7 ? 'label-yellow' : ''}
          {points < 3 ? 'label-red' : ''}">

          <h4 title="progress of the situation in the last 7 days">
            {points.toFixed(0)} / 10
          </h4>
        </div>

      </div>
    </div>
  </div>
</div>
