<script>
  import data from "jhucsse.covid";
  import getCountryISO2 from "country-iso-3-to-2";

  let res;
  var c_infected = [];
  var c_healthy = [];

  function init() {
    data.all().then(function(result) {
      res = result;
      let all = res.confirmed.locations;
      var all_order = [];
      
      console.log(Object.keys(countries_bounds).length);

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

      console.log(all_order.length);

      for (var country of Object.entries(countries_bounds)) {
        if (
          res.confirmed.locations.find(
            e => e.country_code === getCountryISO2(country[1].id)
          ) == undefined
        )
          c_healthy.push([country[0],getCountryISO2(country[1].id)]);
        else c_infected.push([country[0],getCountryISO2(country[1].id)]);
      }
    });
  }
</script>

<style>
  .container-countries{

    position: absolute;
    transform: translateY(0px);
    opacity: 1;
        -webkit-transition-duration: .4s;
    -moz-transition-duration: .4s;
    -o-transition-duration: .4s;
    transition-duration: .4s;

    
  }
    .container-countries.hidden {
   transform: translateY(600px);
    opacity: 0;
  }
  .container-list label{
    width: 40px;
  }
  .container-list .flag{
    width: 35px;
  }
  .container-basic{
      width: calc(50vw - 18px);
      bottom: 12px;
      position: absolute;
      left: 12px;

  }
  .container-list-group{
    display: inline-block;
    width: 49%;
    vertical-align: top;

  }
  .container-body{
    height:400px; 
    overflow:scroll;
  }
</style>

<svelte:head>
  <script src="./js/bounds.js" on:load={init()}>

  </script>
</svelte:head>

{#if !res}
  Loading...
{:else}
<div class="container-basic container-countries hidden">
  <div class="container-list-group">
    <div class="container-header">
      <div class="container-header-contents">
        <h5 class="container-title">Infected Countries</h5> 
        <div style="float:right">
          {c_infected.length}
        </div>
      </div>
    </div>
    <div class="container-body" >
      <div class="container-list">

      {#each c_infected as item, i}
        <li>
            <div class="flag">
              <img
                  src="flags/{item[1] ? item[1]  : 'world'}.png"
                  alt="flag" />
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
        <div style="float:right">
          {c_healthy.length}
        </div>
      </div>
    </div>
    <div class="container-body">
      <div class="container-list">
      {#each c_healthy as item, i}
        <li>
            <div class="flag">
              <img
                  src="flags/{item[1] ? item[1] : 'world'}.png"
                  alt="flag" />
            </div>
            <p class="list-name">{item[0]}</p>
          </li>
      {/each}
      </div>
    </div>
  </div>
</div>
{/if}
