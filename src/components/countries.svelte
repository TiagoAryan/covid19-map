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
          c_healthy.push(country[0]);
        else c_infected.push(country[0]);
      }
    });
  }
</script>

<style>
  section {
    position: absolute;
  }
</style>

<svelte:head>
  <script src="./js/bounds.js" on:load={init()}>

  </script>
</svelte:head>

{#if !res}
  Loading...
{:else}
  <section style="right:0">
    <h1>Total ({c_infected.length + c_healthy.length})</h1>
    <h2>Infected ({c_infected.length})</h2>
    {#each c_infected as item, i}
      <li>{i + 1}: {item}</li>
    {/each}
    <h2>Healthy ({c_healthy.length})</h2>
    {#each c_healthy as item, i}
      <li>{i + 1}: {item}</li>
    {/each}
  </section>
{/if}
