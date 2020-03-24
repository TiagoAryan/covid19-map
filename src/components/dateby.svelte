<script>
  import data from "jhucsse.covid";
  import * as population from "./country-by-population.json";

  export let date;

  let res;
  let all;

  function getContent() {
    data.all().then(function(result) {
      res = result;

      for (let i = 0; i < Object.values(res.deaths.locations).length; i++) {
        console.log(res.deaths.locations[i].history[date]);
        res.deaths.locations[i].date = res.deaths.locations[i].history[date];
      }
    });
  }

  getContent();
</script>

<style>

</style>

{#if !res}Loading...{:else}...{/if}
