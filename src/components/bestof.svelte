<script>
  import { onMount } from "svelte";
  import { s, flag } from "misc";
  import BestofLine from "../components/bestofline.svelte";

  export let data;
  export let show;

  let box_title = "Total";
  let btn_text = "Last Day";
  var list_mode;
  var show_options="hidden";
  let all, length;
  let all_sorted = [];

  $: show, run();

  function run() {
    if (show) {
      length = Object.keys(data.confirmed.locations[0].history).length;

      all_sorted = [];
      if (show == "deaths") all = data.deaths.locations;
      else if (show == "confirmed") all = data.confirmed.locations;
      else if (show == "recovered") all = data.recovered.locations;

      if (list_mode == "last") {
        all_sorted = all.sort(function(a, b) {
          return (
            b.latest -
            Object.values(b.history)[length - 2] -
            (a.latest - Object.values(a.history)[length - 2])
          );
        });
      } else if (list_mode == "growth") {
        var all_red = all.filter(e => {
          return Object.values(e.history)[length - 1] > 100;
        });
        all_sorted = all_red.sort(function(a, b) {
          return (
            ((Object.values(b.history)[length - 1] -
              Object.values(b.history)[length - 2]) *
              100) /
              Object.values(b.history)[length - 2] -
            ((Object.values(a.history)[length - 1] -
              Object.values(a.history)[length - 2]) *
              100) /
              Object.values(a.history)[length - 2]
          );
        });
      } else {
        all_sorted = all.sort(function(a, b) {
          return b.latest - a.latest;
        });
      }
    }
  }
  
  function changeListDisplay(mode) {
    if (mode == "total") {
      list_mode = "total";
      box_title = "Total";
      btn_text = "Total";
    } else if (mode == "last") {
      list_mode = "last";
      box_title = "Last Day";
      btn_text = "Last Day";
    } else if (mode == "growth") {
      list_mode = "growth";
      box_title = "Growth % (min 100)";
      btn_text = "Growth %";
    }
    show_options = "hidden";

    run();
  }
  function toggleOptions(){
     if (show_options == "hidden") {
      show_options = "show";
    } else {
      show_options = "hidden";
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
    z-index: 100;
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
  .container-body {
    padding: 0px;
  }
  .dropdown{
    display: inline-block;
    float: right;
    position: relative;
    
  }
  .dropdown .trigger{
    padding: 6px 12px;
    background-color: transparent;
    border:1px solid white;
    color:white;
    opacity: 0.6;
    -webkit-transition-duration: 0.2s;
    -moz-transition-duration: 0.2s;
    -o-transition-duration: 0.2s;
    transition-duration: 0.2s;
    border-radius: 3px;
  }
  .dropdown .trigger:hover{
    cursor: pointer;
    opacity: 1;
  }
  .trigger i{
    margin-left: 4px;
  }
  .options{
    overflow: hidden;
    position: absolute;
    top: 40px;
    z-index: 1;
    display: block;
    width: 100%;
    
    border-radius: 3px;
    -webkit-transition-duration: 0.2s;
    -moz-transition-duration: 0.2s;
    -o-transition-duration: 0.2s;
    transition-duration: 0.2s;
    background-color: rgba(0,0,0,0.8);
  }
  .options.hidden{
    height: 0px;
    opacity: 0;
  }
  .options.show{
    height: 120px;
    opacity:1;
  }
  .option{
    padding: 8px 16px;
    -webkit-transition-duration: 0.2s;
    -moz-transition-duration: 0.2s;
    -o-transition-duration: 0.2s;
    transition-duration: 0.2s;
  }
  .option:hover{
    background-color: rgba(255,255,255,0.2);
    cursor: pointer;

  }
  .container-header label{
    display: block;
  }
</style>

<div
  id="bestof_{show}"
  class="container-basic container-bestof {show ? '' : 'hidden'}">
  <div class="container-header">
    <div class="container-header-contents">

      <h5 class="container-title">Most {show}</h5>
      <div class="dropdown">
        <div class="trigger" on:click={() => toggleOptions()}>Order by {btn_text}<i class="fas fa-chevron-down" ></i></div>
        <div class="options {show_options}">
          <div
            class="option"
            on:click={() => changeListDisplay('total')}>
            <i class="fas fa-battery-full"></i>
            Total
          </div>
          <div
            class="option"
            on:click={() => changeListDisplay('last')}>
            <i class="fas fa-chart-bar"></i>
            Last Day
          </div>
          <div
            class="option"
            on:click={() => changeListDisplay('growth')}>
            <i class="fas fa-chart-line"></i>
            Growth %
          </div>
        </div>
      
      </div>
     
      <label>— {box_title}</label>
    </div>
  </div>
  <div class="container-body">
    <div class="container-list">
      {#each all_sorted.slice(0, 10) as item, i}
        <BestofLine {show} {list_mode} data={all_sorted} {length} {item} {i} />
      {/each}
    </div>
  </div>
</div>
