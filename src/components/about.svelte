<script>
  import { createEventDispatcher } from "svelte";
  import moment from "moment";
  export let data;
  export let view_about;
  $: view_about;

  const dispatch = createEventDispatcher();

  let btn_style = "";
  let btn_text = "About";
  let btn_icon = "fa-eye";

  function toggleAbout() {
    if (!view_about) {
      btn_style = "secondary";
      btn_text = "Hide";
      btn_icon = "fa-eye-slash";
    } else {
      btn_style = "";
      btn_text = "About";
      btn_icon = "fa-eye";
    }
  }

  function achange() {
    dispatch("achange");
  }
</script>

<style>
  .block-about {
    position: fixed;
    right: 12px;
    bottom: 112px;
    width: 310px;
    height: 140px;
    z-index: 10;
    text-align: right;

    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }
  .block-about.false {
    transform: translateX(330px);
  }
  .block-about.true {
    transform: translateX(0px);
  }
  .button {
    margin-left: 12px;
    margin-bottom: 4px;
  }
  .container-body {
    position: relative;
    display: block;
    width: 100%;
  }
  .container-basic {
    margin: 4px 0;
    width: 310px;
    position: relative;
    display: block;
  }
  .about {
    display: inline-block;
    width: 100%;
    vertical-align: top;
  }
  label {
    display: inline-block;
    width: 100%;
    margin-bottom: 4px;
    font-size: 0.7rem;
  }
  .container-body label {
    width: 30%;
    text-align: left;
    display: inline-block;
    vertical-align: top;
  }
  .container-body p {
    width: 65%;
    text-align: right;
    display: inline-block;
    margin: 0;
    font-size: 0.8rem;
    color: whitesmoke;
  }
  .icon {
    bottom: 20px;
    display: inline-block;
    position: fixed;
    width: auto;
    right: 12px;
    margin: 0;
    cursor: pointer;
  }
  .container-icon i {
    display: inline-block;
  }
  @media (max-width: 768px) {
    .block-about {
      display: none;
    }
  }
</style>

<i class="icon fas fa-info-circle" on:click={achange} />

<div class="block-about {view_about}">
  <div class="container-basic">
    <div class="container-body">
      <label>Covid 19 Information Map</label>
      <time datetime={data.confirmed.last_updated}>
        Last Updated {moment(data.confirmed.last_updated).fromNow()}
      </time>
      <label>Data Sources</label>
      <p>
        <a
          href="https://github.com/ExpDev07/coronavirus-tracker-api"
          target="_blank">
          github:ExpDev07/coronavirus-tracker-api
        </a>
      </p>
      <p>
        <a href="https://github.com/NovelCOVID/API" target="_blank">
          github:NovelCOVID/API
        </a>
      </p>
      <p>
        <a href="https://nextstrain.org/ncov" target="_blank">
          nextstrain.org/ncov
        </a>
      </p>

      <p>
        <a href="https://newsapi.org/" target="_blank">News API</a>
      </p>
      <label>Code at</label>
      <p>
        <a href="https://github.com/TiagoAryan/covid19-map" target="_blank">
          github:TiagoAryan/covid19-map
        </a>
      </p>
      <label>
        Made with
        <i class="fas fa-heart" />
        by
      </label>
      <p>
        <a href="">Tiago Aryan</a>
        &
        <a href="">Beatriz Diogo</a>
      </p>
      <p>
        <a href="/about" target="_blank">More About</a>
      </p>
    </div>
  </div>
</div>
