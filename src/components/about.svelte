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
    right: 10px;
    bottom: 42px;
    width: 350px;
    height: 175px;
    z-index: 10;
    text-align: right;

    -webkit-transition: all 0.4s ease-in-out;
    -moz-transition: all 0.4s ease-in-out;
    -o-transition: all 0.4s ease-in-out;
    transition: all 0.4s ease-in-out;
  }
  .block-about.false {
    transform: translateX(360px);
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
    width: 350px;
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
  .container-row {
    text-align: left;
  }
  h5 {
    text-align: left;
    margin-bottom: 12px;
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
  a:hover {
    color: rgba(255, 255, 255, 1);
    cursor: pointer;
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

  @media (max-width: 768px) {
    .block-about,
    .icon {
      display: none;
    }
  }
</style>

<i class="icon fas fa-info-circle" on:click={achange} />

<div class="block-about {view_about}">
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
          <a class="button secondary" href="/covid19/about/" target="_blank">
            More About
          </a>
        </p>
      </div>
    </div>
  </div>
</div>
