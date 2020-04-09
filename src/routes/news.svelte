<script>
  import { onMount } from "svelte";
  import { NovelCovid } from "novelcovid";
  import moment from "moment";
  import { s } from "misc";

  let data;
  let pop_total, confirmed, deaths, recovered;

  getContent();

  async function getContent() {
    let all = await new NovelCovid().all();

    pop_total = all.cases;
    confirmed = all.active;
    deaths = all.deaths;
    recovered = all.recovered;

    //&sources=cnbc,nbc-news,cbs-news,time,independent,bbc-news,google-news-uk,google-news

    //al-jazeera-english,associated-press,bbc-news,breitbart-news,independent,time

    const response = await fetch(
      "https://newsapi.org/v2/everything?q=coronavirus%20virus%20world%20covid%2019&language=en&sources=al-jazeera-english,associated-press,bbc-news,breitbart-news,independent,time&sortBy=publishedAt&apiKey=db91ea828da64f4fa54ee12fdf7ea095"
    );
    data = await response.json();
  }
</script>

<style>
  .block-news {
    width: 70%;
    margin: 0 auto;
  }
  .pane-loading {
    position: fixed !important;
    width: 100vw;
    height: 100vh;
    z-index: 100;
    background-color: rgba(0, 0, 0, 0.6);
    color: white;
    text-align: center;
    padding: 20vh 0px;
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
    opacity: 1;
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
  .news {
    display: inline-block;
    width: 84%;
    vertical-align: top;
  }
  .img {
    height: auto;
    display: inline-block;
    vertical-align: top;
    width: 15%;
    padding: 5px;
  }
  img {
    width: 100%;
  }
  h3 {
    font-weight: 300;
    margin: 0px 0px 10px 0px;
  }
  p {
    margin: 10px 0;
    font-weight: 300;
    line-height: 1.1rem;
  }
  a {
    color: inherit;
    text-decoration: inherit;
  }
  tt {
    display: inline-block;
    width: 100%;

    font-size: 0.7rem;
  }
  @media (max-width: 768px) {
    .block-news {
      width: 98%;
    }
    .container-body {
      padding: 0px 8px;
    }
    .img {
      height: auto;
      display: block;
      vertical-align: top;
      width: 100%;
      padding: 0px;
    }
  }
</style>

<svelte:head>
  <title>Covid 19 News</title>
</svelte:head>

{#if data}
  <div class="block-news">
    <h3>News</h3>
    <a href="/">
      <div class="container-basic" style=" padding: 40px 0">
        <div class="container-body">
          <div class="container-body body-infected">

            <div class="container-data-details">
              <div class="col-block">
                <i class="dot dot_green" />
                <label>Recovered</label>
                <div class="data">{s(recovered)}</div>
              </div>
              <div class="col-block">
                <i class="dot dot_yellow" />
                <label>Active</label>
                <div class="data">{s(confirmed)}</div>
              </div>
              <div class="col-block">
                <i class="dot dot_red" />
                <label>Deaths</label>
                <div class="data">{s(deaths)}</div>
              </div>
            </div>
            <div class="progress">
              <div
                class="progress-bar"
                role="progressbar"
                style="width: {recovered ? (recovered * 100) / pop_total : 0}%"
                aria-valuenow={recovered ? (recovered * 100) / pop_total : 0}
                aria-valuemin="0"
                aria-valuemax="100" />
              <div
                class="progress-bar bg-warning"
                role="progressbar"
                style="width: {confirmed ? (confirmed * 100) / pop_total : 0}%"
                aria-valuenow={confirmed ? (confirmed * 100) / pop_total : 0}
                aria-valuemin="0"
                aria-valuemax="100" />
              <div
                class="progress-bar bg-danger"
                role="progressbar"
                style="width: {deaths ? (deaths * 100) / pop_total : 0}%"
                aria-valuenow={deaths ? (deaths * 100) / pop_total : 0}
                aria-valuemin="0"
                aria-valuemax="100" />
            </div>
            <label class="progress_label">{s(pop_total)}</label>
          </div>
        </div>
      </div>
    </a>
    {#each data.articles as item, i}
      <a href={item.url} target="_blank" class="more">
        <div class="container-basic">
          <div class="container-body">
            <div class="img">
              <img src={item.urlToImage} alt="img" />
            </div>
            <div class="news">
              <tt>{item.source.name} - {moment(item.publishedAt).fromNow()}</tt>
              <h5>{item.title}</h5>
              <p>{item.description}</p>
            </div>
          </div>
        </div>
      </a>
    {/each}
  </div>
{:else}
  <div class="pane-loading">
    <p>Getting Data</p>
    <div class="lds-roller">
      <div />
      <div />
      <div />
      <div />
      <div />
      <div />
      <div />
      <div />
    </div>
  </div>
{/if}
