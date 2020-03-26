<script>
  import { onMount } from "svelte";
  import data from "jhucsse.covid";
  import getCountryISO2 from "country-iso-3-to-2";
  import Total from "../components/total.svelte";
  import Bestof from "../components/bestof.svelte";
  import Countries from "../components/countries.svelte";

  var map;
  var selected_country, selected_country_id;
  var colors = ["#FFC831", "#FF4E34", "#40C0A5"];
  var circle_size = 8000;
  var circle = [];
  var c = 0;
  let country_clicked, country_name_clicked;
  let res;
  let pop_total = 7772494610;

  let showdate = "00/00/00";
  let inPlay = true;

  function play() {
    console.log(res);
    let length = Object.keys(res.confirmed.locations[0].history).length;
    let dates = Object.keys(
      res.confirmed.locations.filter(e => "Hubei" === e.province)[0].history
    ).sort(function(a, b) {
      return new Date(a) - new Date(b);
    });

    let ii = 0;
    const interval = setInterval(() => {
      var layers = [];
      //clean layers
      map.eachLayer(function(layer) {
        if( layer instanceof L.Circle  )
          layer.remove();
        if( layer instanceof L.Polygon  )
          layer.remove();

      });
      let date = new Date(dates[ii]);
      showdate =
        ("0" + date.getDate()).slice(-2) +
        "/" +
        ("0" + (date.getMonth() + 1)).slice(-2) +
        "/" +
        date.getFullYear();
        if(ii==0){
          InfectedCountries(res, dates[ii]);

        }
        //InfectedCountries(res, dates[ii]);
        placeAllCircles(res, dates[ii]);

      ii++;

      if (ii >= length) {
        clearInterval(interval);
        inPlay = false;
      }
    }, 10);

    return () => {
      clearInterval(interval);
    };
  }

  function playhistory() {
    if (!inPlay) {
      inPlay = true;
      play();
    }
  }

function InfectedCountries(res, date) {
    var i = 0;
    var c = 0;
    var data=res.confirmed;
   
    
     for (var k = 0; k < data.locations.length; k++) {
        var country_name = data.locations[k].country;
        var country_code = data.locations[k].country_code;
        var country_in_map = countries_bounds[country_name];

        //if map is not found in bounds.js by name find it by country code
        if (country_in_map === undefined) {
          for (var country of Object.entries(countries_bounds)) {
            var id_c = country[1].id;
            if (getCountryISO2(id_c) == country_code) {
              var country_in_map = country[1];
              break;
            }
          }
        }
        //if country found
        if (country_in_map !== undefined) {
          var number_people = data.locations[k].history[date];
          var country_json = L.geoJson(country_in_map);

          if(number_people>0){
            var country_json = L.geoJson(country_in_map);

            country_json.getLayers()[0].options.fillColor = "#F7B500";
            country_json.getLayers()[0].options.color = "#F7B500";
            country_json.getLayers()[0].options.fillOpacity = "0.1";
            country_json.getLayers()[0].options.weight = "1";
            country_json.getLayers()[0].options.opacity = "0.4";

            
            map.addLayer(country_json);

          }
        }
     }
     

}

function placeAllCircles(res, date) {
    var i = 0;
    var c = 0;
    var data;
    for (var r = 0; r < 3; r++) {
      if (r == 0) data = res.confirmed;
      else if (r == 1) data = res.deaths;
      else data = res.recovered;

      for (var k = 0; k < data.locations.length; k++) {
        var country_name = data.locations[k].country;
        var country_code = data.locations[k].country_code;
        var country_in_map = countries_bounds[country_name];

        //if map is not found in bounds.js by name find it by country code
        if (country_in_map === undefined) {
          for (var country of Object.entries(countries_bounds)) {
            var id_c = country[1].id;
            if (getCountryISO2(id_c) == country_code) {
              var country_in_map = country[1];
              break;
            }
          }
        }
        //if country found
        if (country_in_map !== undefined) {
          var number_people_prev=0;
          var number_people = data.locations[k].history[date]-number_people_prev;
          var num_circles = parseInt(number_people / 300);
          i = 0;

          //initialize circle for each location
          circle.push({
            country: country_in_map.id,
            circles_green: [],
            circles_yellow: [],
            circles_red: []
          });

          var bound = L.latLngBounds(L.geoJson(country_in_map).getBounds());
          var center_country = bound.getCenter();
          var x_max = bound.getEast();
          var x_min = bound.getWest();
          var y_max = bound.getSouth();
          var y_min = bound.getNorth();
          var j = 0;
          while (j < num_circles) {
            var cor = colors[r];
            //random positions
            var lat = y_min + Math.random() * (y_max - y_min);
            var lng = x_min + Math.random() * (x_max - x_min);
            var point_pos = L.latLng(lat, lng);

            //circle visuals
            var circleOptions = {
              color: cor,
              fillColor: cor,
              fillOpacity: 0.3,
              weight: 1,
              interactive: false
            };

            if (country_in_map.geometry.type == "MultiPolygon") {
              for ( var m = 0; m < country_in_map.geometry.coordinates.length; m++ ) {
                if (inside(lat, lng, country_in_map.geometry.coordinates[m][0])) {
                  if (r == 0) {
                    circle[k].circles_yellow.push(
                      new L.Circle(point_pos, circle_size, circleOptions)
                    );
                    circle[k].circles_yellow[i].addTo(map);
                  } else if (r == 1) {
                    circle[k].circles_green.push(
                      new L.Circle(point_pos, circle_size, circleOptions)
                    );
                    circle[k].circles_green[i].addTo(map);
                  } else {
                    circle[k].circles_red.push(
                      new L.Circle(point_pos, circle_size, circleOptions)
                    );
                    circle[k].circles_red[i].addTo(map);
                  }
                  i++;
                  j++;
                }
              }
            } else {
              if (inside(lat, lng, country_in_map.geometry.coordinates[0])) {
                if (r == 0) {
                  circle[k].circles_yellow.push(
                    new L.Circle(point_pos, circle_size, circleOptions)
                  );
                  circle[k].circles_yellow[i].addTo(map);
                } else if (r == 1) {
                  circle[k].circles_green.push(
                    new L.Circle(point_pos, circle_size, circleOptions)
                  );
                  circle[k].circles_green[i].addTo(map);
                } else {
                  circle[k].circles_red.push(
                    new L.Circle(point_pos, circle_size, circleOptions)
                  );
                  circle[k].circles_red[i].addTo(map);
                }
                i++;
                j++;

              }
            }
         
        }
      }
    }
  }
}
  function init() {
    map = L.map("map", {
      minZoom: 3,
      maxZoom: 8
    }).setView([20, 0], 2.5);
    var popup = new L.Popup();

    var gl = L.mapboxGL({
      attribution:
        '<a href="https://www.maptiler.com/copyright/" target="_blank">© MapTiler</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">© OpenStreetMap contributors</a>',
      accessToken:
        "pk.eyJ1IjoiYmpkaW9nbyIsImEiOiJjazg3bW40dnkwbjYwM2htbWc1NnBidzQ2In0.lh4trQ8-6vDRegpJWs6mBw",
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
      var clicked_in_country = 0;
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
              var add = true;
              if (selected_country != null) {
                if (selected_country_id == country[1].id) {
                  add = false;
                }
                map.removeLayer(selected_country);
              }
              if (add) {
                clicked_in_country++;

                selected_country = country_json;
                selected_country_id = country[1].id;
                country_json.getLayers()[0].options.fillColor = "#F7B500";
                country_json.getLayers()[0].options.color = "#F7B500";
                country_json.getLayers()[0].options.fillOpacity = "0.5";

                map.addLayer(country_json);
                var country_id_3 = country[1].id;
                country_clicked = getCountryISO2(country_id_3);
                country_name_clicked = country[1].properties.name;

                break;
              }
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
            var add = true;
            if (selected_country != null) {
              if (selected_country_id == country[1].id) {
                add = false;
              }
              map.removeLayer(selected_country);
            }
            if (add) {
              clicked_in_country++;
              selected_country = country_json;
              selected_country_id = country[1].id;
              country_json.getLayers()[0].options.fillColor = "#F7B500";
              country_json.getLayers()[0].options.color = "#F7B500";
              country_json.getLayers()[0].options.fillOpacity = "0.5";

              map.addLayer(country_json);
              var country_id_3 = country[1].id;
              country_clicked = getCountryISO2(country_id_3);
              country_name_clicked = country[1].properties.name;
              break;
            }
          }
        }
      }
      if (clicked_in_country == 0) {
        map.removeLayer(selected_country);
        country_clicked = "";
        country_name_clicked = "";
        selected_country_id = "";
      }
    }

    data.all().then(function(result) {
      res = result;

      let all = res.confirmed.locations,
        all_order = [];

      all.reduce(function(res, value) {
        if (!res[value.country]) {
          res[value.country] = {
            country: value.country,
            country_code: value.country_code,
            latest: 0,
            history: {}
          };
          all_order.push(res[value.country]);
        }
        res[value.country].latest += value.latest;
        let H_total = res[value.country].history;

        for (var [key, h] of Object.entries(value.history))
          H_total[key] = (H_total[key] || 0) + value.history[key];

        res[value.country].history = H_total;
        return res;
      }, {});

      play();
    });
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
</script>

<style>
  #map {
    position: fixed !important;
    width: 100vw;
    height: 100vh;
  }
</style>

<svelte:head>
  <script src="./js/bounds.js" on:load={init()}>

  </script>
  <title>Sapper project template</title>
</svelte:head>

<div id="map" />

<Total country={country_clicked} name={country_name_clicked} />
<div class="container-date">
  <div class="date">{showdate}</div>
  <div class="navigate-time">
    <div class="button secondary adj-left">
      <i class="fas fa-chevron-left" />
    </div>
    <div class="button secondary adj-right">
      <i class="fas fa-chevron-right" />
    </div>
    <button class="button" on:click={() => playhistory()} disabled={inPlay}>
      <i class="fas fa-play" />
    </button>
  </div>
</div>

<Bestof type="deaths" />
<Countries />
