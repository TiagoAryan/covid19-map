<script>
  import moment from "moment";
  export let data;
  let view_news="hidden";
  let btn_style= "";
  let btn_text= "About";
  let btn_icon= "fa-eye";


  function toggleNews() {
    if (view_news == "hidden") {
      view_news = "show";
      btn_style="secondary";
      btn_text= "Hide";
      btn_icon= "fa-eye-slash";
    } else {
      view_news = "hidden";
      btn_style="";
      btn_text= "About";
      btn_icon= "fa-eye";
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
  .block-about.hidden{
    bottom: -100px;

  }
  .block-about.show{
    bottom: 12px;

  }
  .button{
    margin-left:12px;
    margin-bottom:4px;
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
  h4 {
    display: inline-block;
    width: auto;
    font-weight: 300;
    margin: 0px 0px 10px 0px;
  }
  .container-body label{
    width:30%;
    text-align:left;
    display: inline-block;
    vertical-align: top;
  }
  .container-body p{
    width:65%;
    text-align:right;
    display: inline-block;
    margin:0;
    font-size: 0.8rem;
    color:whitesmoke;
  }
  @media (max-width: 768px) {
    .block-about {
      display: none;
    }
  }
</style>

<div class="block-about {view_news}">
  <div class="button secondary"  on:click={() => toggleNews()}><i class="far {btn_icon}"></i> {btn_text}</div>
  
  <div class="container-basic">
    <div class="container-body">
      <label> Last Updated</label>
      <p> 12/04/2020</p>
      <label> Sources</label>
      <p> 12/04/2020</p>
      <label> Sources</label>
      <p> 12/04/2020</p>
      <label>Credits</label>
      <p> <a href="">Tiago Aryan</a> & <a href="">Beatriz Diogo</a> </p>
    </div>
  </div>
</div>
