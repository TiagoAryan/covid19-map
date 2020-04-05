<script>
  import * as population from "./country-by-population.json";
  import { s, flag } from "misc";

  export let country;
  let days=123;
  let res;

  $: setpop = false;



</script>

<style>
  .container-stats {
    position: relative;
    display: inline-block;
    margin: 0;
    margin-bottom: 12px;
    height:110px;
    width: 100%;
    left: 0;
    vertical-align: top;
  }
  .col-block{
    width: 32%;
  }
  h4{
    margin: 0px
  }
  .label-big h4{
    display: inline-block;
    margin: 0;
    font-weight: 600;
  }
  .label-big{
    padding: 6px 12px;
    border-radius: 6px;
    display: inherit;
  } 
  .label-yellow{
    color: #ffc831;
    background-color: rgba(255, 200, 49, 0.2) ;
  }
  .label-red{
    color:  #ff4e34;
    background-color: rgba(255, 78, 52, 0.2) ;
  }
  .label-green{
   color: #40c0a5;
    background-color: rgba(64, 192, 165, 0.2);
  }
  .col-block label{
    display: block;
    margin-bottom: 4px
  }
</style>

<div class="container-basic container-stats">

  <div class="container-body">

    <div class="container-data-details">
      <div class="col-block">
        <label>Days since first infected</label>
        <h4> {days} Days</h4>
      </div>
      <div class="col-block">
          <label>Fatality</label>
        <div class="label-big label-yellow">
        
          <h4> 2.4%</h4>
        </div>
      </div>
      <div class="col-block">
        <label>Current Situation</label>
        <div class="label-big label-green">

        <h4> 2 / 10</h4>
        </div>

      </div>
    </div>
  </div>
</div>
