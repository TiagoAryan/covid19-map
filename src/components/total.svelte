<script>
  import data from "jhucsse.covid";
  import * as population from "./country-by-population.json";

  export let country;

  let res;
  let pop_total, deaths, confirmed, recovered;

  let setpop = false;

  if (country) {
    data.getCountry({ cc: country }).then(function(result) {
      res = result;
      console.log(result);

      res.deaths = Object.values(res.deaths);
      res.confirmed = Object.values(res.confirmed);
      res.recovered = Object.values(res.recovered);

      deaths = res.deaths[res.deaths.length - 1];
      confirmed = res.confirmed[res.confirmed.length - 1];
      recovered = res.recovered[res.recovered.length - 1];
      pop_total = deaths + confirmed + recovered;
      console.log(pop_total);
    });
  } else {
    data.all().then(function(result) {
      res = result;
      console.log(result);

      pop_total = 7772494610;
      deaths = res.latest.deaths;
      confirmed = res.latest.confirmed;
      recovered = res.latest.recovered;
    });
  }

  function findPop(items, attribute, value) {
    for (var i = 0; i < items.length; i++) {
      if (items[i][attribute] === value) {
        return items[i].z * 1000;
      }
    }
    return null;
  }

  function change() {
    if (setpop) {
      setpop = false;
      pop_total = deaths + confirmed + recovered;
    } else {
      setpop = true;
      pop_total = findPop(population.default, "code", country);
    }
  }
</script>

<style>
  h2 {
    color: white;
  }

  th,
  td {
    padding: 2px 10px;
    color: white;
  }
</style>

{#if !res}
  Loading...
{:else}

  <h2>{country ? country : 'World'} - {pop_total}</h2>
  <table>
    <thead>
      <tr>
        <th scope="col">Deaths</th>
        <th scope="col">Confirmed</th>
        <th scope="col">Recovered</th>
        <th scope="col" />
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>{deaths}</td>
        <td>{confirmed}</td>
        <td>{recovered}</td>
        <td on:click={change()}>change</td>
      </tr>
    </tbody>
  </table>
  <div class="progress">
    <div
      class="progress-bar bg-danger"
      role="progressbar"
      style="width: {(deaths * 100) / pop_total}%"
      aria-valuenow={(deaths * 100) / pop_total}
      aria-valuemin="0"
      aria-valuemax="100" />
    <div
      class="progress-bar bg-warning"
      role="progressbar"
      style="width: {(confirmed * 100) / pop_total}%"
      aria-valuenow={(confirmed * 100) / pop_total}
      aria-valuemin="0"
      aria-valuemax="100" />
    <div
      class="progress-bar"
      role="progressbar"
      style="width: {(recovered * 100) / pop_total}%"
      aria-valuenow={(recovered * 100) / pop_total}
      aria-valuemin="0"
      aria-valuemax="100" />
  </div>
{/if}
