<script>
  import { onMount } from "svelte";
  import Total from "../components/total.svelte"

  function init() {
    var selected_country;
    var map = L.map("map", {
      minZoom: 3,
      maxZoom: 4
    }).setView([20, 0], 3);
    var popup = new L.Popup();

    var gl = L.mapboxGL({
      attribution:
        '<a href="https://www.maptiler.com/copyright/" target="_blank">© MapTiler</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">© OpenStreetMap contributors</a>',
      accessToken: "not-needed",
      zoom: 2,
      style:
        "https://api.maptiler.com/maps/d553177c-3d94-4f4e-9f2a-a7aed3f55787/style.json?key=TLbKST4hnYUY3nc3yvDh"
    }).addTo(map);

    // Location to centre the map
    var center = new L.LatLng(20, 0);
    map.setView(center, 3);

    // Location of the marker
    var markerLocation = new L.LatLng(51.5, -0.09);

    var marker = new L.Marker(markerLocation);
    map.addLayer(marker);

    map.on("click", onMapClick);

    //Listener function taking an event object
    function onMapClick(e) {
      var click_pos = L.latLng(e.latlng.lat, e.latlng.lng);
      var bound = L.latLngBounds(L.geoJson(country).getBounds());

      for (var country of Object.entries(countries_bounds)) {
        var country_json = L.geoJson(country);
        var country_bounds = country_json.getBounds();
        var bound = L.latLngBounds(country_bounds);
        if (bound.contains(click_pos)) {
          console.log(country[0]);

          //add layer in map
          if (selected_country != null) {
            map.removeLayer(selected_country);
          }
          selected_country = country_json;

          map.addLayer(country_json);
          break;
        }
      }
    }
  }

  onMount(() => {
    setTimeout(() => init(), 50);
  });
</script>

<style>
  #map {
    position: fixed !important;
    width: 100vw;
    height: 100vh;
  }

  .text {
    position: absolute;
    width: 100vw;
    height: 100vh;
    pointer-events: none;
  }

  .total {
	padding-top: 85vh;
    width: 45vw;
    margin: 0 auto;
  }
</style>

<svelte:head>
  <title>Sapper project template</title>
</svelte:head>

<div id="map" />

<div class="text">
  <div class="total">
	<Total/>
    <a href="/about" rel="prefecht">about</a>
  </div>
</div>
