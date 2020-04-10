<script>
  import moment from "moment";
  export let data;
</script>

<style>
  .block-news {
    position: fixed;
    left: 12px;
    bottom: 12px;
    width: 310px;
    z-index: 10;
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
  .news {
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
  h4 {
    display: inline-block;
    width: 85%;
    font-weight: 300;
    margin: 0px 0px 10px 0px;
  }
  a {
    color: inherit;
    text-decoration: inherit;
  }
  @media (max-width: 768px) {
    .block-news {
      display: none;
    }
  }
</style>

<div class="block-news">
  <h4>News</h4>
  {#each data.slice(0, 3) as item, i}
    <a href="/news" rel="prefetch">
      <div class="container-basic">
        <div class="container-body">
          <label>{item.source.name} - {moment(item.publishedAt).fromNow()}</label>
          <div class="news">{item.title}</div>
        </div>
      </div>
    </a>
  {/each}
</div>
