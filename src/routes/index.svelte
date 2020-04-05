<script>
  import { onMount } from "svelte";
  import data from "jhucsse.covid";
  import getCountryISO2 from "country-iso-3-to-2";
  import Info from "../components/info.svelte";
  import Bestof from "../components/bestof.svelte";
  import Countries from "../components/countries.svelte";
  import Chart from "../components/chart.svelte";
  import Details from "../components/details.svelte";

  var map, gl;
  var selected_country, selected_country_id;
  var colors = ["#FFC831", "#FF4E34", "#40C0A5"];
  var circle_size = 8000;
  var circle = [];
  var c = 0;
  let country_clicked,
    country_name_clicked = "World";
  let res;
  let pop_total = 7772494610;
  var missing_country = [];
  var n_lines = 0;
  let c_infec_lines = [];
  let geo_infec_lines = [];
  let showdate = "00/00/00";
  let bounds;
  let show = "";
  let show_details = "hidden";
  let inPlay = true;

  let show_info = "show";

  function play() {
    let length = Object.keys(res.confirmed.locations[0].history).length;
    let dates = Object.keys(res.confirmed.locations[0].history).sort(function(
      a,
      b
    ) {
      return new Date(a) - new Date(b);
    });

    if (n_lines) {
      for (var x = 0; x < n_lines; x++) {
        gl._glMap.removeLayer("route_draw_" + x);
        gl._glMap.removeLayer("point_" + x);
        gl._glMap.removeSource("route_draw_" + x);
        gl._glMap.removeSource("point_" + x);
        gl._glMap.removeSource("route_" + x);
      }
      n_lines = 0;
      missing_country = [];
    }

    let ii = 0;
    const interval = setInterval(() => {
      var layers = [];

      //clean layers
      map.eachLayer(function(layer) {
        if (layer instanceof L.Circle) layer.remove();
        if (layer instanceof L.Polygon) layer.remove();
      });
      let date = new Date(dates[ii]);
      showdate =
        ("0" + date.getDate()).slice(-2) +
        "/" +
        ("0" + (date.getMonth() + 1)).slice(-2) +
        "/" +
        date.getFullYear();
      InfectedCountries(res, dates[ii]);
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
    var i = 0,
      c = 0;
    var data = res.confirmed;
    var data_rec = res.recovered;

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

        if (number_people > 0) {
          var number_people_rec = data_rec.locations[k].history[date];
          var people_rec = data_rec.locations[k].country;

          var country_json = L.geoJson(country_in_map);
          if (number_people_rec < number_people - number_people_rec) {
            //yellow
            country_json.getLayers()[0].options.fillColor = "#FFC831";
            country_json.getLayers()[0].options.color = "#FFC831";
          } else {
            //green
            country_json.getLayers()[0].options.fillColor = "#40C0A5";
            country_json.getLayers()[0].options.color = "#40C0A5";
          }

          country_json.getLayers()[0].options.fillOpacity = "0.05";
          country_json.getLayers()[0].options.weight = "1";
          country_json.getLayers()[0].options.opacity = "0";

          map.addLayer(country_json);

          if (c_infec_lines.indexOf(country_name) != "-1") {
            if (missing_country.indexOf(country_name) == -1) {
              missing_country.push(country_name);

              if (
                geo_infec_lines[c_infec_lines.indexOf(country_name)] !== null
              ) {
                var from =
                  geo_infec_lines[c_infec_lines.indexOf(country_name)][0];
                var to =
                  geo_infec_lines[c_infec_lines.indexOf(country_name)][1];
                drawline(n_lines++, from, to);
              }
            }
          }
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
          var number_people_prev = 0;
          var number_people =
            data.locations[k].history[date] - number_people_prev;
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
              for (
                var m = 0;
                m < country_in_map.geometry.coordinates.length;
                m++
              ) {
                if (
                  inside(lat, lng, country_in_map.geometry.coordinates[m][0])
                ) {
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

  async function init() {
    bounds = countries_bounds;
    map = L.map("map", {
      minZoom: 3,
      maxZoom: 8
    }).setView([20, 0], 2.5);
    var popup = new L.Popup();

    gl = L.mapboxGL({
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
      show = "";
      show_info = "show";
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
                country_json.getLayers()[0].options.fillColor = "#FFF";
                country_json.getLayers()[0].options.color = "#FFF";
                country_json.getLayers()[0].options.fillOpacity = "0.2";

                map.addLayer(country_json);
                var country_id_3 = country[1].id;
                country_clicked = getCountryISO2(country_id_3);
                country_name_clicked = country[1].properties.name;

                //map.fitBounds(country_json.getBounds());
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
              country_json.getLayers()[0].options.fillColor = "#FFF";
              country_json.getLayers()[0].options.color = "#FFF";
              country_json.getLayers()[0].options.fillOpacity = "0.2";

              map.addLayer(country_json);
              var country_id_3 = country[1].id;
              country_clicked = getCountryISO2(country_id_3);
              country_name_clicked = country[1].properties.name;
              //map.fitBounds(country_json.getBounds());

              break;
            }
          }
        }
      }
      if (clicked_in_country == 0) {
        map.removeLayer(selected_country);
        country_clicked = "";
        country_name_clicked = "World";
        selected_country_id = "";
      }
    }

    gl._glMap.loadImage("img/plane.png", function(error, image) {
      if (error) throw error;
      gl._glMap.addImage("plane", image);
    });

    getSpread();

    await data.all().then(function(result) {
      res = result;

      res.confirmed.locations = sort(res.confirmed.locations);
      res.deaths.locations = sort(res.deaths.locations);
      res.recovered.locations = sort(res.recovered.locations);

      play();
    });
  }

  function sort(all) {
    let all_order = [];

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

    return all_order.sort((a, b) =>
      a.country > b.country ? 1 : b.country > a.country ? -1 : 0
    );
  }

  function inside(y, x, vs) {
    // ray-casting algorithm based on
    // http://www.ecse.rpi.edu/Homepages/wrf/Research/Short_Notes/pnpoly.html
    var inside = false;
    if (vs.length > 0) {
      for (var i = 0, j = vs.length - 1; i < vs.length; j = i++) {
        var xi = vs[i][0],
          yi = vs[i][1];
        var xj = vs[j][0],
          yj = vs[j][1];
        var intersect =
          yi > y != yj > y && x < ((xj - xi) * (y - yi)) / (yj - yi) + xi;
        if (intersect) inside = !inside;
      }
    }
    return inside;
  }

  function showList(scope) {
    if (!show) show = scope;
    else if (show != scope) show = scope;
    else show = "";
  }

  async function getSpread() {
    const response = await fetch("./ncov.json");
    let data = await response.json();

    let tree = data.tree;
    let geo = data.meta.geo_resolutions[1].demes;

    eachSpread(tree, geo);
  }

  let n = 0;
  function eachSpread(o, p) {
    for (var k in o.children) {
      if (Object.keys(o.children[k]).length > 0) {
        let c = true;
        if (o.node_attrs.division != null)
          if (o.children[k].node_attrs.division != null)
            if (
              o.node_attrs.division.value ===
              o.children[k].node_attrs.division.value
            )
              c = false;
        if (c)
          if (
            o.node_attrs.division &&
            o.children[k].node_attrs.division &&
            o.children[k].node_attrs.country
          ) {
            if (
              c_infec_lines.indexOf(o.children[k].node_attrs.country.value) ==
              "-1"
            ) {
              var from = [
                p[o.node_attrs.division.value].longitude,
                p[o.node_attrs.division.value].latitude
              ];
              var to = [
                p[o.children[k].node_attrs.division.value].longitude,
                p[o.children[k].node_attrs.division.value].latitude
              ];

              c_infec_lines.push(o.children[k].node_attrs.country.value);
              geo_infec_lines.push([from, to]);
            }
          }
        eachSpread(o.children[k], p);
      }
    }
  }

  function drawline(k, from, to) {
    console.log("draw");
    var speedFactor = 30; // number of frames per longitude degree
    var animation; // to store and cancel the animation
    var startTime = 0;
    var progress = 0; // progress = timestamp - startTime
    var resetTime = false; // indicator of whether time reset is needed for the

    var line_options = {
      color: "#FFC831",
      smoothFactor: 10,
      dashArray: "4 4",
      weight: 1
    };

    var route = {
      type: "FeatureCollection",
      features: [
        {
          type: "Feature",
          geometry: {
            type: "LineString",
            coordinates: [from, to]
          }
        }
      ]
    };

    // Coordinates are initially set to origin.
    var point = {
      type: "FeatureCollection",
      features: [
        {
          type: "Feature",
          properties: {},
          geometry: {
            type: "Point",
            coordinates: from
          }
        }
      ]
    };
    var lineDistance = turf.lineDistance(route.features[0], "kilometers");
    var arc = [];
    var steps = 8;

    // Draw an arc between the `origin` & `destination` of the two points
    for (var i = 0; i < lineDistance; i += lineDistance / steps - 1) {
      var segment = turf.along(route.features[0], i, "kilometers");
      arc.push(segment.geometry.coordinates);
    }
    // Update the route with calculated arc coordinates
    route.features[0].geometry.coordinates = arc;

    var route_draw = {
      type: "FeatureCollection",
      features: [
        {
          type: "Feature",
          geometry: {
            type: "LineString",
            coordinates: [from]
          }
        }
      ]
    };

    var counter = 0;

    gl._glMap.addSource("route_" + k, {
      type: "geojson",
      data: route
    });
    gl._glMap.addSource("route_draw_" + k, {
      type: "geojson",
      data: route_draw
    });

    gl._glMap.addSource("point_" + k, {
      type: "geojson",
      data: point
    });

    gl._glMap.addLayer({
      id: "route_draw_" + k,
      source: "route_draw_" + k,
      type: "line",
      paint: {
        "line-width": 1,
        "line-color": "rgba(255, 255, 255, 0.5)",
        "line-dasharray": [3, 6]
        
      }
      
    });

    gl._glMap.addLayer({
      id: "point_" + k,
      source: "point_" + k,
      type: "symbol",
      layout: {
        "icon-image": "plane",
        "icon-rotate": -50,
        "icon-rotation-alignment": "map",
        "icon-allow-overlap": true,
        "icon-ignore-placement": true
      }
    });
    // Start the animation.
    animate(counter);

    /*  document.getElementById("replay").addEventListener("click", function() {
      // Set the coordinates of the original point back to origin
      point.features[0].geometry.coordinates = from;
      route_draw.features[0].geometry.coordinates = [from];

      // Update the source layer
      gl._glMap.getSource("point_" + k).setData(point);
      gl._glMap.getSource("route_draw_" + k).setData(route_draw);

      // Reset the counter
      counter = 0;

      // Restart the animation.
      animate(counter);
    }); */

    function animate() {
      // Update point geometry to a new position based on counter denoting
      // the index to access the arc.
      if(counter+1<route.features[0].geometry.coordinates.length){
      point.features[0].geometry.coordinates =
        route.features[0].geometry.coordinates[counter+1];
      }else{
         point.features[0].geometry.coordinates =
        route.features[0].geometry.coordinates[counter];
      }

      // Calculate the bearing to ensure the icon is rotated to match the route arc
      // The bearing is calculate between the current point and the next point, except
      // at the end of the arc use the previous point and the current point
      point.features[0].properties.bearing = turf.bearing(
        turf.point(
          route.features[0].geometry.coordinates[
            counter >= steps ? counter - 1 : counter
          ]
        ),
        turf.point(
          route.features[0].geometry.coordinates[
            counter >= steps ? counter : counter + 1
          ]
        )
      );
      route_draw.features[0].geometry.coordinates.push(
        route.features[0].geometry.coordinates[counter]
      );
      gl._glMap.getSource("route_draw_" + k).setData(route_draw);

      // Update the source with this new data.
      gl._glMap.getSource("point_" + k).setData(point);

      // Request the next frame of animation so long the end has not been reached.
      if (counter < steps) {
        requestAnimationFrame(animate);
      }
      counter = counter + 1;
    }
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
  <title>Covid 19 Info</title>
</svelte:head>

<div id="map" />

<div class="container-date">
  <div class="date">{showdate}</div>
  <div class="navigate-time">
    <div class="button secondary adj-left">
      <i class="fas fa-chevron-left" />
    </div>
    <div class="button secondary adj-right">
      <i class="fas fa-chevron-right" />
    </div>
    <button
      class="button {inPlay}"
      on:click={() => playhistory()}
      disabled={inPlay}>
      <i class="fas fa-play" />
    </button>
  </div>
</div>
<div class="container-icons">
  <div
    class="container-basic container-icon"
    on:click={() => showList('confirmed')}>
    <i class="fas fa-procedures" />
  </div>
  <div
    class="container-basic container-icon"
    on:click={() => showList('recovered')}>
    <i class="fas fa-notes-medical" />
  </div>
  <div
    class="container-basic container-icon"
    on:click={() => showList('deaths')}>
    <i class="fas fa-user-times" />
  </div>
</div>

{#if res !== undefined}
  <Info
    data={res}
    country={country_clicked}
    name={country_name_clicked}
    {show_info} />
  <Bestof data={res} {show} />
  <Details
    data={res}
    {bounds}
    country={country_clicked}
    name={country_name_clicked}
    {show_details} />
{/if}
