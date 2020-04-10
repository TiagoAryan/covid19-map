<script>
  import moment from "moment";
  export let data;
  export let view_about;
  $: view_about;
  
  let btn_style= "";
  let btn_text= "About";
  let btn_icon= "fa-eye";


  function toggleNews() {
    view_about= !view_about;
    if (!view_about) {
      btn_style="secondary";
      btn_text= "Hide";
      btn_icon= "fa-eye-slash";
    } else {
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
  .block-about.false{
    transform: translateX(330px);

  }
  .block-about.true{
    transform: translateX(0px);

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
  .container-icon{
    bottom:20px;
    display: inline-block;
    position: fixed;
    width: auto;
    right: 12px;
    margin:0;
  }
  .container-icon i{
    display: inline-block;
  }
  @media (max-width: 768px) {
    .block-about {
      display: none;
    }
  }
</style>

      <div
        class="container-basic container-icon"
         on:click={() => toggleNews()}>
        <i class="fas fa-info"></i>
</div>
<div class="block-about {view_about}">  
  <div class="container-basic">
    <div class="container-body">
      <label> Last Updated</label>
      <p> 12/04/2020</p>
      <label> Sources</label>
      <a href="https://github.com/ExpDev07/coronavirus-tracker-api" target="_blank"> ExpDev07 - coronavirus-tracker-api</a>
      <label> Sources</label>
      <a href="https://github.com/NovelCOVID/API" target="_blank"> NovelCOVID API</a>
      <label>Credits</label>
      <p> <a href="">Tiago Aryan</a> & <a href="">Beatriz Diogo</a> </p>
    </div>
  </div>
</div>
