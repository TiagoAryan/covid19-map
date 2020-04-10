<script>
  import { createEventDispatcher } from "svelte";
  import moment from "moment";
  export let data;
  export let view_news;

  const dispatch = createEventDispatcher();

  $: view_news, toggleNews();

  let btn_icon;
  let full_news = false;

  function toggleNews() {
    if (!view_news) {
      btn_icon = "newspaper";
    } else {
      btn_icon = "eye";
    }
  }

  function toggleFullNews() {
    if (!full_news) nchange();
    full_news = !full_news;
  }

  function nchange() {
    dispatch("nchange");
  }
</script>

<style>
  section {
    background-color: #18181a;
    position: relative;
    z-index: 22;
  }
  .block-news {
    position: fixed;
    left: 12px;
    bottom: 72px;
    width: 310px;
    z-index: 10;

    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }
  .full-block-news {
    width: 100%;
    max-width: 800px;
    margin: 0 auto 20px auto;
  }
  .full-block-news .more:hover h5 {
    text-decoration: underline;
  }
  .block-news .container-basic {
    -webkit-transition: all 0.4s ease;
    -moz-transition: all 0.4s ease;
    -o-transition: all 0.4s ease;
    transition: all 0.4s ease;
  }
  .block-news.hidden .container-basic {
    transform: translateX(-330px);
  }
  .block-news.show .container-basic {
    transform: translateX(0px);
  }
  .block-news .container-basic:nth-of-type(1) {
    -webkit-transition-duration: 0.6s;
    -moz-transition-duration: 0.6s;
    -o-transition-duration: 0.6s;
    transition-duration: 0.6s;
  }
  .block-news .container-basic:nth-of-type(2) {
    -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }
  .block-news .container-basic:nth-of-type(3) {
    -webkit-transition-duration: 0.2s;
    -moz-transition-duration: 0.2s;
    -o-transition-duration: 0.2s;
    transition-duration: 0.2s;
  }
  .button {
    margin-left: 12px;
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
  .full-container-basic {
    margin: 4px 0;
    position: relative;
    display: block;
    background-color: #1e1e21;
    box-shadow: 3px 3px 10px black;
    padding: 16px 0px 16px 0px;
  }
  .news {
    display: inline-block;
    width: 100%;
    vertical-align: top;
  }
  .full-news {
    display: inline-block;
    width: 80%;
    vertical-align: top;
  }
  label {
    display: inline-block;
    width: 100%;
    margin-bottom: 4px;
    font-size: 0.7rem;
  }
  h3 {
    display: inline-block;
    width: auto;
    font-weight: 300;
    margin: 0px 0px 10px 0px;
  }
  a {
    color: inherit;
    text-decoration: inherit;
  }
  .container-icon {
    bottom: 12px;
    display: inline-block;
    position: fixed;
    width: auto;
    left: 12px;
    margin: 0;
  }
  .container-icon i {
    margin-right: 6px;
    display: inline-block;
  }
  .container-icon p {
    display: inline-block;
  }
  figure {
    position: static;
    display: block;
    bottom: auto;
    left: auto;
    z-index: 1;
    float: right;
    overflow: hidden;
    border-radius: 8px;
  }
  img {
    object-fit: cover;
    width: 100px;
    height: 100px;
  }
  .button_close {
    float: right;
  }

  @media (max-width: 768px) {
    .block-news {
      display: none;
    }
    .full-block-news {
      width: 96%;
    }
    .container-body {
      padding: 0px 8px;
    }
    .full-news {
      width: 75%;
    }
    figure,
    img {
      width: 80px;
      height: 80px;
    }
    .full-block-news .more:first-of-type figure,
    .full-block-news .more:first-of-type img {
      width: 100%;
      height: 200px;
    }
    .full-block-news .more:first-of-type .full-news {
      width: 100%;
    }
  }
  time::before {
    content: "\0000a0\002022\0000a0";
  }
</style>

<div class="container-basic container-icon" on:click={nchange}>
  <i class="fas fa-{btn_icon}" />
  <p>News</p>
</div>
<div class="block-news {view_news ? '' : 'hidden'}">
  {#each data.slice(0, 3) as item, i}
    <div
      class="container-basic"
      on:click={() => toggleFullNews()}
      style="cursor: pointer;">
      <div class="container-body">
        <div class="news">{item.title}</div>
        <label>
          {item.source.name}
          <time datetime={item.publishedAt}>
            {moment(item.publishedAt).fromNow()}
          </time>
        </label>
      </div>
    </div>
  {/each}
</div>

{#if full_news}
  <section>
    <div class="full-block-news">
      <h3>News</h3>
      <div
        class="button secondary button_close"
        on:click={() => toggleFullNews()}>
        <i class="fas fa-times" />
        Close
      </div>

      {#each data as item, i}
        <a href={item.url} target="_blank" class="more">
          <div class="full-container-basic">
            <div class="container-body">
              <figure>
                <img src={item.urlToImage} alt="img" />
              </figure>
              <div class="full-news">
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
  </section>
{/if}
