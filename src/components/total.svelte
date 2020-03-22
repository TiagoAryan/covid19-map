<script >
  import data from "jhucsse.covid";

    let res
    let pop_total = 7772494610;

    data.all().then(function(result) {
        res = result
        console.log(result);
    })

</script>

<style>

    h2{
        color: white;
    }

    th,td {
        padding: 2px 10px;
        color: white;
    }
</style>

{#if !res}
  Loading...
{:else}

<h2>World</h2>
<table>
  <thead>
    <tr>
      <th scope="col">Deaths</th>
      <th scope="col">Confirmed</th>
      <th scope="col">Recovered</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>{res.latest.deaths}</td>
      <td>{res.latest.confirmed}</td>
      <td>{res.latest.recovered}</td>
    </tr>
  </tbody>
</table>
<div class="progress">
    <div class="progress-bar bg-danger" role="progressbar" style="width: {res.latest.deaths*100/pop_total}%" aria-valuenow="{res.latest.deaths*100/pop_total}" aria-valuemin="0" aria-valuemax="100"></div>
    <div class="progress-bar bg-warning" role="progressbar" style="width: {res.latest.confirmed*100/pop_total}%" aria-valuenow="{res.latest.confirmed*100/pop_total}" aria-valuemin="0" aria-valuemax="100"></div>
    <div class="progress-bar" role="progressbar" style="width: {res.latest.recovered*100/pop_total}%" aria-valuenow="{res.latest.recovered*100/pop_total}" aria-valuemin="0" aria-valuemax="100"></div>
</div>
{/if} 