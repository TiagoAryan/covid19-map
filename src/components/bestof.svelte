<script>
  import data from "jhucsse.covid";
  import * as population from "./country-by-population.json";

  export let type;

  let res;
  let all, all_sorted;

  function getContent() {
    data.all().then(function(result) {
      res = result;

      if (type == "deaths") all = res.deaths.locations;
      else if (type == "confirmed") all = res.confirmed.locations;
      else if (type == "recovered") all = res.recovered.locations;

      var all_order = [];
      all.reduce(function(res, value) {
        if (!res[value.country]) {
          res[value.country] = { country: value.country, latest: 0 };
          all_order.push(res[value.country]);
        }
        res[value.country].latest += value.latest;
        return res;
      }, {});

      all_sorted = all_order.sort(function(a, b) {
        return b.latest - a.latest;
      });
    });
  }

  getContent();
</script>

<style>
  section {
    position: absolute;
  }
</style>

{#if !res}
  Loading...
{:else}
  <section>
    {#each all_sorted.slice(0, 10) as item, i}
      <li>{i + 1}: {item.country} ({item.latest})</li>
    {/each}
  </section>
{/if}
