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
    width: 100%;
    max-width: 800px;
    margin: 0 auto 20px auto;
  }
  .block-news .more:hover h5 {
    text-decoration: underline;
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
  .pane-loading p {
    margin-bottom: 20px;
    margin-top: 20px;
    font-size: 2.6rem;
    font-weight: 300;
    color: whitesmoke;
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
    width: 80%;
    vertical-align: top;
  }
  figure {
    position: static;
    display: block;
    bottom: auto;
    left: auto;
    z-index: 1;
    width: 100px;
    height: 100px;
    overflow: hidden;
    float: right;
    border-radius: 8px;
  }
  img {
    object-fit: cover;
    width: 100px;
    height: 100px;
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
  label {
    font-size: 0.7rem;
  }
  @media (max-width: 768px) {
    .block-news {
      width: 96%;
    }
    .container-body {
      padding: 0px 8px;
    }
    .news {
      width: 75%;
    }
    figure,
    img {
      width: 80px;
      height: 80px;
    }
    .block-news .more:first-of-type figure,
    .block-news .more:first-of-type img {
      width: 100%;
      height: 200px;
    }
    .block-news .more:first-of-type .news {
      width: 100%;
    }
  }
  section {
    padding: 4rem 0px;
  }
  .button {
    position: fixed;
    top: 12px;
    left: 12px;
    z-index: 100;
  }
  .button i {
    margin-right: 4px;
  }
  time::before {
    content: "\0000a0\002022\0000a0";
  }
</style>

<svelte:head>
  <title>Covid 19 News</title>
</svelte:head>
<section>

  {#if data}
    <div class="block-news">
      <a href="/" class="button secondary">
        <i class="fas fa-chevron-left" />
        Go Back to Map
      </a>

      <h3>Current World Situation</h3>
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
    </div>
    <div class="block-news">

      <h3>News</h3>

      {#each data.articles as item, i}
        <a href={item.url} target="_blank" class="more">
          <div class="container-basic">
            <div class="container-body">
              <figure>
                <img src={item.urlToImage} alt="img" />
              </figure>
              <div class="news">
                <h5>{item.title}</h5>
                <label>
                  {item.source.name}
                  <time datetime={item.publishedAt}>
                    {moment(item.publishedAt).fromNow()}
                  </time>
                </label>
                <p class="d-none d-sm-none d-sx-none">{item.description}</p>
              </div>
              <p class="d-sm-block d-sx-block">{item.description}</p>
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
</section>
