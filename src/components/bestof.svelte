<script>
  import { onMount } from "svelte";
  import { s, flag } from "misc";

  export let data;
  export let show;

  let box_title="Total";
  let btn_icon= "fa-list-ul";
  let btn_text= "Last Day";
  let list_mode=true;

  let all, length;
  let all_sorted = [],
    all_order = [];

  $: show && run(list_mode);

  function run(r) {
    length = Object.keys(data.confirmed.locations[0].history).length;

    all_sorted = [];
    all_order = [];
    if (show == "deaths") all = data.deaths.locations;
    else if (show == "confirmed") all = data.confirmed.locations;
    else if (show == "recovered") all = data.recovered.locations;

    if(r){
       all_sorted = all.sort(function(a, b) {
        return b.latest - a.latest;
      });
    }else{
      all_sorted = all.sort(function(a, b) {
        return b.latest - a.latest;
      });
    }
    
  }
  function changeListDisplay(){
    if (list_mode) {
      list_mode = false;
      run(false);
      box_title="Last Day";
      btn_icon= "fa-layer-group";
      btn_text= "Total";
    } else {
      run(true);
      list_mode = true;
      box_title="Total";
      btn_icon= "fa-list-ul";
      btn_text= "Last Day";
    }
  }
</script>

<style>
  .container-bestof {
    position: fixed;
    left: 84px;

    width: 380px;
    height: 460px;

    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;

    pointer-events: all;
  }
  .container-bestof.hidden {
    height: 0px;
    pointer-events: none;
    opacity: 0;
  }
  .container-bestof .container-body {
    max-height: 420px;
    overflow: scroll;
  }

  #bestof_confirmed {
    top: 100px;
  }

  #bestof_recovered {
    top: 172px;
  }
  #bestof_deaths {
    top: 244px;
  }
  .container-body{
    padding:0px;
  }
  .container-body li{
    padding:3px 24px;
  }
  .list_green{
    background-color:#40C0A51a;
  }
  .list_green label{
    color: #40C0A5;
    font-weight:500;
    opacity:1;
  }
  .list_green label i{
    color: #40C0A5;

  }

  .list_red{
    background-color:#ff4e341a;
  }
  .list_red label{
    color: #FF4E34;
    font-weight:500;
    opacity:1;
  }
  .list_red label i{
    color: #FF4E34;

  }
</style>

<div
  id="bestof_{show}"
  class="container-basic container-bestof {show ? '' : 'hidden'}">
  <div class="container-header">
    <div class="container-header-contents">

      <h5 class="container-title">Most {show} </h5>
      <div
          style="float:right"
          class="button"
          on:click={() => changeListDisplay()}>
          <i class="fas {btn_icon}" />
          {btn_text}
        </div>
         <label>— {box_title}</label>
    </div>
  </div>
  <div class="container-body">
    <div class="container-list">
      {#each all_sorted.slice(0, 10) as item, i}
          <li class="{Object.values(all_sorted[i].history)[length - 2] < Object.values(all_sorted[i + 1].history)[length - 2] ? "list_red" :""} 
              {i > 0 && Object.values(all_sorted[i].history)[length - 2] > Object.values(all_sorted[i - 1].history)[length - 2] ? "list_green" :""} ">
      
          <label>
            {#if Object.values(all_sorted[i].history)[length - 2] < Object.values(all_sorted[i + 1].history)[length - 2]}
              <i class="fas fa-sort-up" />
            {:else if i > 0 && Object.values(all_sorted[i].history)[length - 2] > Object.values(all_sorted[i - 1].history)[length - 2]}
              <i class="fas fa-sort-down" />
            {:else}#{/if}
            {i + 1}
          </label>
          <div class="flag">
            <img src={flag(item.country)} alt="flag" />
          </div>
          <p class="list-name">{item.country}</p>
          <p class="list-count">
            <span class="badge badge-light">
              + {s(item.latest - Object.values(item.history)[length - 2])}
            </span>
            {s(item.latest)}
          </p>
        </li>
      {/each}
    </div>
  </div>
</div>
