<script>
  import { onMount } from "svelte";
  import { s, flag } from "misc";
  import BestofLine from "../components/bestofline.svelte";

  export let data;
  export let show;

  let box_title = "Total";
  let btn_text = "Total";
  var list_mode;
  var show_options = "hidden";
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

      all.sort(function(a, b) {
        return b.latest - a.latest;
      });

      if (list_mode == "today") {
        all_sorted = all.sort(function(a, b) {
          return (
            b.latest -
            Object.values(b.history)[length - 2] -
            (a.latest - Object.values(a.history)[length - 2])
          );
        });
      } else if (list_mode == "last7") {
        all_sorted = all.sort(function(a, b) {
          return b.last7 - a.last7;
        });
      } else if (list_mode == "last30") {
        all_sorted = all.sort(function(a, b) {
          return b.last30 - a.last30;
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
    } else if (mode == "today") {
      list_mode = "today";
      box_title = "Today";
      btn_text = "Today";
    } else if (mode == "growth") {
      list_mode = "growth";
      box_title = "Today Growth ( >100 cases )";
      btn_text = "Today Growth";
    } else if (mode == "last7") {
      list_mode = "last7";
      box_title = "Last 7 Days";
      btn_text = "Last 7 Day";
    } else if (mode == "last30") {
      list_mode = "last30";
      box_title = "Last 30 Days";
      btn_text = "Last 30 Days";
    }
    show_options = "hidden";

    run();
  }
  function toggleOptions() {
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
  .dropdown {
    min-width: 165px;
    display: inline-block;
    float: right;
    position: relative;
  }
  .dropdown .trigger {
    padding: 6px 10px;
    background-color: transparent;
    border: 1px solid white;
    color: white;
    opacity: 0.6;
    -webkit-transition-duration: 0.2s;
    -moz-transition-duration: 0.2s;
    -o-transition-duration: 0.2s;
    transition-duration: 0.2s;
    border-radius: 3px;
  }
  .dropdown .trigger:hover {
    cursor: pointer;
    opacity: 1;
  }
  .options {
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
    background-color: rgba(0, 0, 0, 0.8);
  }
  .options.hidden {
    height: 0px;
    opacity: 0;
  }
  .options.show {
    opacity: 1;
  }
  .option {
    padding: 8px 16px;
    -webkit-transition-duration: 0.2s;
    -moz-transition-duration: 0.2s;
    -o-transition-duration: 0.2s;
    transition-duration: 0.2s;
  }
  .option:hover {
    background-color: rgba(255, 255, 255, 0.2);
    cursor: pointer;
  }
  .container-header label {
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
        <div class="trigger" on:click={() => toggleOptions()}>
          <i class="fas fa-filter" style="margin-right: 4px;" />
          {btn_text}
          <i class="fas fa-chevron-down" style="float: right; margin: 4% 0;" />
        </div>
        <div class="options {show_options}">
          <div class="option" on:click={() => changeListDisplay('total')}>
            <i class="fas fa-battery-full" />
            Total
          </div>
          <div class="option" on:click={() => changeListDisplay('today')}>
            <i class="fas fa-chart-bar" />
            Today
          </div>
          <div class="option" on:click={() => changeListDisplay('growth')}>
            <i class="fas fa-chart-line" />
            Today Growth
          </div>
          <div class="option" on:click={() => changeListDisplay('last7')}>
            <i class="fas fa-chart-bar" />
            Last 7 Days
          </div>
          <div class="option" on:click={() => changeListDisplay('last30')}>
            <i class="fas fa-chart-bar" />
            Last 30 Days
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
