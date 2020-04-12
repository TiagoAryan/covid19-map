<script>
  import Preview from "../components/preview.svelte";
  import Stats from "../components/stats.svelte";
  import Countries from "../components/countries.svelte";
  import Chart from "../components/chart.svelte";
  import ChartW from "../components/chart_world.svelte";
  import ChartPoints from "../components/chart_points.svelte";
  import Chart100 from "../components/chart_100.svelte";
  import moment from "moment";
  import { isMobile } from "misc";

  export let data;
  export let bounds;
  export let country;
  export let name;
  export let show_details;

  $: show_details;

  var dados = JSON.parse(JSON.stringify(data));
</script>

<style>
  .container_details_box {
    width: calc(100vw - 18px);
    top: 100px;
    height: calc(100vh - 24px);

    left: 12px;
    top: 12px;
    display: inline;
    padding-bottom: 0px;
    margin-right: 0;
    position: absolute;
    transform: translateX(0px);
    opacity: 1;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
    z-index: 11;
  }
  .container_details_box-half {
    left: auto;
    width: calc(50vw - 18px);
    right: 12px;
  }
  .container_details_box.hidden {
    transform: translateY(100vh);
    opacity: 0;
  }
  .container_details {
    width: calc(100vw - 18px);
    height: calc(100vh - 24px);
    position: relative;
    display: block;
  }

  .box-half {
    width: calc(50% - 6px);
    display: inline-block;
    height: 100%;
    position: relative;
    vertical-align: top;
  }
  .box-half-right {
    margin-left: 6px;
  }
  .container_details_box {
    left: auto;
    width: calc(100vw - 24px);
    right: 12px;
  }
  .container_details_box-half {
    left: auto;
    width: calc(100% - 24px);
    max-width: 800px;
    right: 12px;
  }
  .container_details_box-half .box-half-right {
    margin-left: 0px;
  }
  .container-body label {
    width: 30%;
    text-align: left;
    display: inline-block;
    vertical-align: top;
  }
  .container-body p,
  .container-body time {
    width: 68%;
    text-align: right;
    display: inline-block;
    margin: 0;
    font-size: 0.8rem;
    color: whitesmoke;
    line-height: 0.7rem;
    vertical-align: top;
  }
  .container-body p.fit {
    width: 97%;
  }
  a {
    color: rgba(255, 255, 255, 0.8);
    font-weight: 400;
    text-decoration: underline;
    -webkit-transition: all 0.4s ease-in-out;
    -moz-transition: all 0.4s ease-in-out;
    -o-transition: all 0.4s ease-in-out;
    transition: all 0.4s ease-in-out;
  }
  a.button {
    text-decoration: none;
    float: right;
    margin-top: 4px;
  }
  .ico {
    font-size: 1rem;
    padding: 5px;
  }
  @media (max-width: 1280px) {
    .container_details_box-half {
      max-width: 600px;
    }
  }
  @media (max-width: 768px) {
    .box-half {
      width: 100%;
      margin-bottom: 12px;
      height: auto;
    }
    .box-half-right {
      margin-left: 0px;
    }
    .container_details_box {
      top: 4px;
      right: 22px;
    }
    .container_details {
      width: calc(100vw - 2px);
      display: flex;
      flex-flow: column;
    }
    .box-half:nth-of-type(2) {
      order: 1;
      margin-bottom: 0px;
    }
    .box-half:nth-of-type(1) {
      order: 2;
      margin-top: 0px;
    }
  }
</style>

<div
  class="container_details_box {country ? 'container_details_box-half' : ''}
  {!show_details ? 'hidden' : ''}">
  <div class="container_details">
    {#if !country}
      <div class="box-half">
        <ChartW data={dados} />
        <ChartPoints data={dados} />
        {#if isMobile()}
          <div class="block-about">
            <div class="container-basic">
              <div class="container-body">
                <h5>Covid 19 Information Map</h5>

                <div class="container-row">
                  <label>Last Updated</label>
                  <time datetime={data.confirmed.last_updated}>
                    {moment(data.confirmed.last_updated).fromNow()}
                  </time>
                </div>
                <div class="container-row">

                  <label>Data Sources</label>
                  <p>
                    <a
                      href="https://github.com/ExpDev07/coronavirus-tracker-api"
                      target="_blank">
                      github:ExpDev07/coronavirus-tracker-api
                    </a>
                  </p>
                  <p class="fit">
                    <a href="https://github.com/NovelCOVID/API" target="_blank">
                      github:NovelCOVID/API
                    </a>
                  </p>
                </div>
                <div class="container-row">
                  <label>
                    Made with
                    <i class="fas fa-heart" />
                    by
                  </label>
                  <p>
                    <a href="http://sparebullit.com/tiagoaryan">Tiago Aryan</a>
                    &
                    <a href="http://sparebullit.com/btriz">Beatriz Diogo</a>
                  </p>
                </div>
                <div class="container-row">
                  <p class="fit">
                    <a
                      title="Source Code"
                      href="https://github.com/TiagoAryan/covid19-map"
                      target="_blank">
                      <i class="ico fab fa-github" />
                    </a>
                    <a
                      title="Buy me a Coffee"
                      href="https://www.buymeacoffee.com/tiagoaryan"
                      target="_blank">
                      <i class="ico fas fa-coffee" />
                    </a>
                    <a class="button secondary" href="/about" target="_blank">
                      More About
                    </a>
                  </p>
                </div>
              </div>
            </div>
          </div>
        {/if}
      </div>
    {/if}

    <div
      class="box-half box-half-right {country ? 'container_details_box-half-right' : ''}">
      <Preview data={dados} {country} {name} {show_details} on:cchange />
      {#if country}
        <Stats data={dados} {country} />
        <Chart data={dados} {country} />
      {/if}
      {#if !country}
        <Countries data={dados} {bounds} />
        <Chart100 data={dados} />
      {/if}

    </div>
  </div>
</div>
