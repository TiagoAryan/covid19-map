<script>
  import { NovelCovid } from "novelcovid";
  import moment from "moment";
  let update;
  getContent();
  async function getContent() {
    let all = await new NovelCovid().all();
    update = all.updated;
  }
</script>

<style>
  .block-about {
    width: 100%;
    max-width: 800px;
    margin: 0 auto 20px auto;
  }
  h3 {
    font-weight: 300;
    margin: 0px 0px 10px 0px;
  }

  a {
    color: inherit;
    text-decoration: inherit;
  }
  .container-body {
    position: relative;
    display: block;
    width: 100%;
  }
  .container-basic {
    margin: 4px 0;
    position: relative;
    display: block;
  }
  label {
    display: inline-block;
    margin-bottom: 4px;
    font-size: 1rem;
  }
  .container-body label {
    width: 40%;
    text-align: left;
    display: inline-block;
    vertical-align: top;
  }
  .container-body p,
  .container-body .time {
    width: 58%;
    text-align: right;
    display: inline-block;
    margin: 0;
    font-size: 0.9rem;
    color: whitesmoke;
    line-height: 0.8rem;
    vertical-align: top;
  }
  .container-body p.fit {
    width: 98%;
  }
  @media (max-width: 768px) {
    .block-about {
      width: 96%;
    }
  }
  section {
    padding: 4rem 0px;
  }
  .go-back {
    position: fixed;
    top: 12px;
    left: 12px;
    z-index: 100;
  }
  .go-back i {
    margin-right: 4px;
  }
  .container-header {
    display: block;
    border-bottom: 1px solid rgba(151, 151, 151, 0.3);
    width: 100%;
    padding-bottom: 14px;
    margin-bottom: 16px;
  }
  .container-body {
    display: block;
    padding: 0px 24px;
  }
  .container-header h5{
    padding:0px 24px;
    display: inline-block;
  }
  .subtitle{
    padding: 6px 0px;
    margin-top: 24px;
    margin-bottom: 12px;
    border-bottom: 1px solid rgba(151, 151, 151, 0.3);

  }
</style>

<svelte:head>
  <title>Covid 19 About</title>
</svelte:head>

<section>

  <div class="block-about">
    <a href="/" class="button secondary go-back">
      <i class="fas fa-chevron-left" />
      Go Back to Map
    </a>
  </div>
  <div class="block-about">

    <h3>About this Project</h3>

    <div class="container-basic">
      <div class="container-header">
       <h5>
          Covid 19 Information Map
        </h5>
          <label>v1</label>

      </div>
      <div class="container-body">
       

        <div class="container-row">
          <label>Last Updated</label>
          {#if update}
            <time class="time" datetime={new Date(update)}>
              {moment(update).fromNow()}
            </time>
          {/if}
        </div>
        <div class="container-row">

          <h5 class="subtitle">Sources</h5>
          <label>Data</label>
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
          <label>
            Transmissions (Last update:
            <time datetime={new Date('03/26/2020')}>
              {moment('03/26/2020').fromNow()}
            </time>
            )
          </label>
          <p>
            <a href="https://nextstrain.org/ncov" target="_blank">Nextstrain</a>
          </p>
          <label>News</label>
          <p>
            <a href="https://newsapi.org/" target="_blank">News API</a>
          </p>

          <label>
            Borders (Last update:
            <time datetime={new Date('03/26/2020')}>
              {moment('03/26/2020').fromNow()}
            </time>
            )
          </label>
          <p>
            <a
              href="https://www.aljazeera.com/news/2020/03/coronavirus-travel-restrictions-border-shutdowns-country-200318091505922.html"
              target="_blank">
              Aljazeera
            </a>
          </p>
          <p class="fit">
            <a
              href="https://www.iatatravelcentre.com/international-travel-document-news/1580226297.htm"
              target="_blank">
              Travel Centre
            </a>
          </p>
        </div>
        <div class="container-row">
          <h5 class="subtitle">Code</h5>

          <label>Repository at</label>
          <p>
            <a href="https://github.com/TiagoAryan/covid19-map" target="_blank">
              github:TiagoAryan/covid19-map
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
            Tiago Aryan
            &
          Beatriz Diogo
          </p>
          <p class="fit">
            <a  class="button secondary" href="https://www.buymeacoffee.com/tiagoaryan" target="_blank">
              Buy me a Coffee
            </a>
          </p>
        </div>
      </div>
    </div>
    <div class="container-basic">
      <div class="container-body">
        <div class="container-row">
          <p class="fit" style="text-align: center;">
            All data and information provided on this site is for informational
            purposes only. I make no representation as to accuracy,
            completeness, currentness, suitability, or validity of any
            information on this site and will not be liable for any errors,
            omissions, or delays in this information or any losses, injuries, or
            damages arising from its display or use. All information is provided
            on an as-is basis.
          </p>
        </div>
      </div>
    </div>
    <div class="container-basic">
      <div class="container-body">
        <div class="container-row">
          <p class="fit" style="text-align: center;">
            Wash Hands. Stay safe. Stay inside.
          </p>

        </div>
      </div>

    </div>
  </div>
</section>
