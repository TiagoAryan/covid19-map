<script>
  import moment from "moment";
  export let data;
  export let view_news;
  $: view_news;

  let btn_style= "";
  let btn_text= "Show News";
  let btn_icon= "fa-eye";

  function toggleNews() {
      view_news = !view_news;

    if (!view_news ) {
      btn_style="secondary";
      btn_text= "Hide News";
      btn_icon= "fa-eye-slash";
    } else {
      btn_style="";
      btn_text= "Show News";
      btn_icon= "fa-eye";
    }
  }
</script>

<style>
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
  .block-news a{
    -webkit-transition: all 0.4s ease;
    -moz-transition: all 0.4s ease;
    -o-transition: all 0.4s ease;
    transition: all 0.4s ease;
  }
  .block-news.false a{
    transform: translateX(-330px);
  }
  .block-news.true a{
    transform: translateX(0px);
  }
  .block-news a:nth-of-type(1){
     -webkit-transition-duration: 0.6s;
    -moz-transition-duration: 0.6s;
    -o-transition-duration: 0.6s;
    transition-duration: 0.6s;
  }
  .block-news a:nth-of-type(2){
     -webkit-transition-duration: 0.4s;
    -moz-transition-duration: 0.4s;
    -o-transition-duration: 0.4s;
    transition-duration: 0.4s;
  }
  .block-news a:nth-of-type(3){
     -webkit-transition-duration: 0.2s;
    -moz-transition-duration: 0.2s;
    -o-transition-duration: 0.2s;
    transition-duration: 0.2s;
  }
  .button{
    margin-left:12px;
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
    width: auto;
    font-weight: 300;
    margin: 0px 0px 10px 0px;
  }
  a {
    color: inherit;
    text-decoration: inherit;
  }
  
  .container-icon{
    bottom:12px;
    display: inline-block;
    position: fixed;
    width: auto;
    left: 12px;
    margin:0;
  }
  .container-icon i{
    margin-right: 6px;
    display: inline-block;
  }
  .container-icon p{
    display: inline-block;
  }
  @media (max-width: 768px) {
    .block-news {
      display: none;
    }
  }
</style>

      <div
        class="container-basic container-icon"
        on:click={() => toggleNews()}>
        <i class="fas fa-newspaper"></i><p>News</p>
</div>
<div class="block-news {view_news}">
  {#each data.slice(0, 3) as item, i}
    <a href="/news" rel="prefetch" class="container-basic">
        <div class="container-body">
          <label>{item.source.name} - {moment(item.publishedAt).fromNow()}</label>
          <div class="news">{item.title}</div>
        </div>
    </a>
  {/each}
</div>
