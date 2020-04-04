<script>
  import getCountryISO2 from "country-iso-3-to-2";
  import { flag } from "misc";

  export let data;
  export let bounds;

  let res;
  var c_infected = [];
  var c_healthy = [];

  res = data;
  let all = res.confirmed.locations;
  var all_order = [];

  all.reduce(function(res, value) {
    if (!res[value.country]) {
      res[value.country] = {
        country: value.country,
        country_code: value.country_code
      };
      all_order.push(res[value.country]);
    }
    return res;
  }, {});

  for (var country of Object.entries(bounds)) {
    if (
      res.confirmed.locations.find(
        e => e.country_code === getCountryISO2(country[1].id)
      ) == undefined
    )
      c_healthy.push([country[0], getCountryISO2(country[1].id)]);
    else c_infected.push([country[0], getCountryISO2(country[1].id)]);
  }
</script>

<style>
  .container-countries {
    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }
  .container-list label {
    width: 40px;
  }
  .container-list .flag {
    width: 35px;
  }
  .container-basic {
    width: 100%;
    height: calc(100% - 202px);
    display: inline-block;
    position: relative;
    overflow: hidden;
  }
  .container-list-group {
    display: inline-block;
    width: 49%;
    vertical-align: top;
    height: 100%;
  }
  .container-body {
    height: calc(100% - 40px);
    overflow: scroll;
  }
</style>

{#if !res}
  Loading...
{:else}
  <div class="container-basic container-countries ">
    <div class="container-list-group">
      <div class="container-header">
        <div class="container-header-contents">
          <h5 class="container-title">Infected Countries</h5>
          <div style="float:right">{c_infected.length}</div>
        </div>
      </div>
      <div class="container-body">
        <div class="container-list">

          {#each c_infected as item, i}
            <li>
              <div class="flag">
                <img src={flag(item[0])} alt="flag" />
              </div>
              <p class="list-name">{item[0]}</p>
            </li>
          {/each}
        </div>
      </div>
    </div>

    <div class=" container-list-group">
      <div class="container-header">
        <div class="container-header-contents">
          <h5 class="container-title">Healthy Countries</h5>
          <div style="float:right">{c_healthy.length}</div>
        </div>
      </div>
      <div class="container-body">
        <div class="container-list">
          {#each c_healthy as item, i}
            <li>
              <div class="flag">
                <img src={flag(item[0])} alt="flag" />
              </div>
              <p class="list-name">{item[0]}</p>
            </li>
          {/each}
        </div>
      </div>
    </div>
  </div>
{/if}
