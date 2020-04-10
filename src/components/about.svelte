<script>
  import moment from "moment";
  export let data;

  let view_news = "hidden";
  let btn_style = "";
  let btn_text = "About";
  let btn_icon = "fa-eye";

  function toggleAbout() {
    if (view_news == "hidden") {
      view_news = "show";
      btn_style = "secondary";
      btn_text = "Hide";
      btn_icon = "fa-eye-slash";
    } else {
      view_news = "hidden";
      btn_style = "";
      btn_text = "About";
      btn_icon = "fa-eye";
    }
  }
</script>

<style>
  .block-about {
    position: fixed;
    right: 12px;
    bottom: 12px;
    width: 310px;
    height: 140px;
    z-index: 10;
    text-align: right;

    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }
  .block-about.hidden {
    bottom: -100px;
  }
  .block-about.show {
    bottom: 12px;
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
  @media (max-width: 768px) {
    .block-about {
      display: none;
    }
  }
</style>

<div class="block-about {view_news}">
  <div
    class="button secondary"
    on:click={() => toggleAbout()}
    title="Last Update {moment(data.confirmed.last_updated).fromNow()}">
    <i class="far {btn_icon}" />
    {btn_text}
  </div>

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
      <label>Credits</label>
      <p>
        <a href="">Tiago Aryan</a>
        <i class="fas fa-heart" />
        <a href="">Beatriz Diogo</a>
      </p>
    </div>
  </div>
</div>
