<script>
  import { onMount } from "svelte";
  import Total from "../components/total.svelte";
  import data from "jhucsse.covid";

  var map;
  var selected_country;
  var colors = ["#FF4E34", "#FFC831", "#40C0A5"];
  var circle_size = 20000;
  var circle = [];
  let country_clicked;

  let res;
  let pop_total = 7772494610;

  data.all().then(function(result) {
    res = result;
    console.log(result);
    var i = 0;
    var c = 0;
    for (var k = 0; k < res.confirmed.locations.length; k++) {
      var country_name = res.confirmed.locations[k].country;
      var country_code = res.confirmed.locations[k].country_code;
      var country_in_map = countries_bounds[country_name];

      if (country_in_map === undefined) {
        for (var country of Object.entries(countries_bounds)) {
          var id_c = country[1].id;
          if (id_c.substr(0, 2) == country_code) {
            var country_in_map = country[1];
            //console.log("FOUND CODE"+res.confirmed.locations[k].country_code);
            break;
          }
        }
      }
      if (country_in_map !== undefined) {
        var random = parseInt(1 + Math.floor(Math.random() * 20));
        var random = parseInt(res.confirmed.locations[k].latest / 1000);
        i = 0;

        //console.log(random);
        var bound = L.latLngBounds(L.geoJson(country_in_map).getBounds());
        var center_country = bound.getCenter();

        var x_max = bound.getEast();
        var x_min = bound.getWest();
        var y_max = bound.getSouth();
        var y_min = bound.getNorth();
        var j = 0;
        circle.push({ country: country_in_map.id, circles: [] });
        while (j < random) {
          var rand_color = parseInt(1 + Math.floor(Math.random() * 100));
          var cor = colors[1];
          var circleOptions = {
            color: cor,
            fillColor: cor,
            fillOpacity: 0.3,
            weight: 1
          };

          var lat = y_min + Math.random() * (y_max - y_min);
          var lng = x_min + Math.random() * (x_max - x_min);
          var point_pos = L.latLng(lat, lng);

          if (country_in_map.geometry.type == "MultiPolygon") {
            for (
              var m = 0;
              m < country_in_map.geometry.coordinates.length;
              m++
            ) {
              //console.log("IS INSIDEEEEE");
              if (inside(lat, lng, country_in_map.geometry.coordinates[m][0])) {
                circle[c].circles.push(
                  new L.Circle(point_pos, circle_size, circleOptions)
                );
                map.addLayer(circle[c].circles[i]);

                i++;
                j++;
              }
            }
          } else {
            //console.log("--------one shape--------")
            if (inside(lat, lng, country_in_map.geometry.coordinates[0])) {
              //console.log("IS INSIDEEEEE");
              circle[c].circles.push(
                new L.Circle(point_pos, circle_size, circleOptions)
              );
              map.addLayer(circle[c].circles[i]);
              i++;
              j++;
            }
          }
        }
        c++;
      }
    }
  });

  function init() {
    map = L.map("map", {
      minZoom: 3,
      maxZoom: 8
    }).setView([20, 0], 2.5);
    var popup = new L.Popup();

    var gl = L.mapboxGL({
      attribution:
        '<a href="https://www.maptiler.com/copyright/" target="_blank">© MapTiler</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">© OpenStreetMap contributors</a>',
      accessToken: "not-needed",
      maxZoom: 8,
      style:
        "https://api.maptiler.com/maps/5ce0b2a2-d5dc-44ae-84f3-7211439b9474/style.json?key=TLbKST4hnYUY3nc3yvDh"
    }).addTo(map);

    map.on("click", onMapClick);

    //Listener function taking an event object
    function onMapClick(e) {
      var click_pos = L.latLng(e.latlng.lat, e.latlng.lng);
      var bound = L.latLngBounds(L.geoJson(country).getBounds());
      var circleOptions = {
        color: "#F7B500",
        fillColor: "#F7B500",
        fillOpacity: 0.3,
        weight: 1
      };
      for (var country of Object.entries(countries_bounds)) {
        var country_json = L.geoJson(country);
        if (country[1].geometry.type == "MultiPolygon") {
          for (var m = 0; m < country[1].geometry.coordinates.length; m++) {
            if (
              inside(
                e.latlng.lat,
                e.latlng.lng,
                country[1].geometry.coordinates[m][0]
              )
            ) {
              if (selected_country != null) {
                map.removeLayer(selected_country);
              }
              selected_country = country_json;
              country_json.getLayers()[0].options.fillColor = "#F7B500";
              country_json.getLayers()[0].options.color = "#F7B500";
              country_json.getLayers()[0].options.fillOpacity = "0.5";

              map.addLayer(country_json);
              var country_id_3 = country[1].id;
              country_clicked = country_id_3.substr(0, 2);

              console.log(country_clicked);
              break;
            }
          }
        } else {
          if (
            inside(
              e.latlng.lat,
              e.latlng.lng,
              country[1].geometry.coordinates[0]
            )
          ) {
            if (selected_country != null) {
              map.removeLayer(selected_country);
            }
            selected_country = country_json;
            country_json.getLayers()[0].options.fillColor = "#F7B500";
            country_json.getLayers()[0].options.color = "#F7B500";
            country_json.getLayers()[0].options.fillOpacity = "0.5";

            map.addLayer(country_json);
            var country_id_3 = country[1].id;
            country_clicked = country_id_3.substr(0, 2);

            console.log(country_clicked);

            break;
          }
        }
      }
    }
  }

  function inside(y, x, vs) {
    // ray-casting algorithm based on
    // http://www.ecse.rpi.edu/Homepages/wrf/Research/Short_Notes/pnpoly.html
    var inside = false;
    for (var i = 0, j = vs.length - 1; i < vs.length; j = i++) {
      var xi = vs[i][0],
        yi = vs[i][1];
      var xj = vs[j][0],
        yj = vs[j][1];
      var intersect =
        yi > y != yj > y && x < ((xj - xi) * (y - yi)) / (yj - yi) + xi;
      if (intersect) inside = !inside;
    }
    return inside;
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
    width: 80%;
    bottom: 0px;
    left: 10%;
  }

  .total {
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
    <Total country={country_clicked} />
    <a href="/about" rel="prefecht">about</a>
  </div>
</div>
