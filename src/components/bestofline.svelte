<script>
  import { s, flag } from "misc";

  export let show;
  export let list_mode;
  export let data;
  export let length;
  export let item;
  export let i;

  let color, icon, title, number;

  $: show, list_mode, run();

  function run() {
    if (
      Object.values(data[i].history)[length - 2] <
      Object.values(data[i + 1].history)[length - 2]
    ) {
      show != "recovered" ? (color = "list_red") : (color = "list_green");
      icon = '<i class="fas fa-sort-up" />';
    } else if (
      i != 0 &&
      Object.values(data[i].history)[length - 2] >
        Object.values(data[i - 1].history)[length - 2]
    ) {
      show == "recovered" ? (color = "list_red") : (color = "list_green");
      icon = '<i class="fas fa-sort-down" />';
    } else {
      color = "";
      icon = "#";
    }

    if (list_mode == "today") {
      title = s(item.latest);
      number = s(item.latest - Object.values(item.history)[length - 2]);
    } else if (list_mode == "last7") {
      title =
        s(item.latest) +
        " (" +
        (((item.latest - item.last7) * 100) / item.last7).toFixed(0) +
        "% )";
      number = s(item.last7);
    } else if (list_mode == "last30") {
      title =
        s(item.latest) +
        " (" +
        (((item.latest - item.last30) * 100) / item.last30).toFixed(0) +
        "% )";
      number = s(item.last30);
    } else if (list_mode == "growth") {
      title =
        s(item.latest) +
        " (+" +
        s(item.latest - Object.values(item.history)[length - 2]) +
        ")";
      let n = 1;
      if (Object.values(item.history)[length - 2] !== 0)
        n = Object.values(item.history)[length - 2];

      number =
        (
          ((Object.values(item.history)[length - 1] -
            Object.values(item.history)[length - 2]) *
            100) /
          n
        ).toFixed(0) + "%";
    } else {
      title = "+" + s(item.latest - Object.values(item.history)[length - 2]);
      number = s(item.latest);
    }
  }
</script>

<style>
  li {
    padding: 3px 24px;
  }
  .list_green {
    background-color: #40c0a51a;
  }
  .list_green label {
    color: #40c0a5;
    font-weight: 500;
    opacity: 1;
  }
  .list_green label i {
    color: #40c0a5;
  }

  .list_red {
    background-color: #ff4e341a;
  }
  .list_red label {
    color: #ff4e34;
    font-weight: 500;
    opacity: 1;
  }
  .list_red label i {
    color: #ff4e34;
  }
  @media (max-width: 768px) {
    .container-bestof{
      left: 70px;
      height: 440px;
      width: calc(100% - 78px);
      max-width: 320px;

    }
    li {
    padding: 3px 18px;
  }
  }
  @media (max-width: 480px) {
    .container-bestof {
       left: 60px;
      height: 380px;
      width: calc(100% - 68px);

    }

  }
</style>

<li class={color}>

  <label>
    {@html icon}
    {i + 1}
  </label>
  <div class="flag">
    <img src={flag(item.country)} alt="flag" />
  </div>
  <p class="list-name">{item.country}</p>
  <p class="list-count">
    <span {title}>{number}</span>
  </p>
</li>
