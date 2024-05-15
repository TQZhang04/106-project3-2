<script>
  import { onMount } from "svelte";
  import * as d3 from "d3";

  let year = 1930;
  let artist = "all";
  let art_data = [];
  let art_color = ["#20fc03", "#f4fc03", "#fca103", "#fc0303", "#03b1fc"];
  const colorScale = d3
    .scaleOrdinal()
    .domain([
      "On View",
      "Installation pending",
      "Temporary Installation",
      "By Appointment",
      "Public Tours",
    ])
    .range(art_color);
  const artFile =
    "https://raw.githubusercontent.com/TQZhang04/106-project3/main/public_art_locations_datasd.csv";
  const max_r = 10;
  let svg, projection, artMarkers;

  function resizeMarkers() {
    const width = document.getElementById("map").clientWidth;
    const height = document.getElementById("map").clientHeight;
    svg.attr("width", width).attr("height", height);

    projection.translate([width / 2, height / 2]);

    artMarkers
      .attr("cx", function (d) {
        return projection([+d.lng, +d.lat])[0];
      })
      .attr("cy", function (d) {
        return projection([+d.lng, +d.lat])[1];
      });
  }

  function updateMarkers() {
    artMarkers
      .transition()
      .duration(200)
      .attr("r", function (d) {
        const artYear = +d.accession_number.slice(0, 4);
        let artist_name = d.artist;
        let artist_group = artist_name.includes("(");
        if (artist_group) {
          artist_name = artist_name.slice(0, artist_name.indexOf("("));
        }
        let artist_list = artist_name.split("|");
        for (let i = 0; i < artist_list.length; i++) {
          artist_list[i] = artist_list[i].trim();
        }
        if (artYear <= year) {
          if (artist == "all" || artist_list.includes(artist)) {
            return max_r;
          }
        }
        return 0;
      });
  }

  onMount(() => {
    const width = document.getElementById("map").clientWidth;
    const height = document.getElementById("map").clientHeight;
    svg = d3
      .select("#map")
      .append("svg")
      .attr("width", width)
      .attr("height", height);

    svg
      .append("defs")
      .append("filter")
      .attr("id", "grayscale")
      .append("feColorMatrix")
      .attr("type", "matrix")
      .attr(
        "values",
        "0.1 0.3333 0.3333 0 0 \
        0.1 0.3333 0.3333 0 0 \
        0.1 0.3333 0.3333 0 0 \
        0     0       0      1 0"
      );

    svg
      .append("image")
      .attr(
        "xlink:href",
        "https://github.com/TQZhang04/106-project3-2/blob/main/San%20Diego%20zoomed2.png?raw=true"
      )
      .attr("width", "100%")
      .attr("height", "100%")
      .attr("filter", "url(#grayscale)");

    projection = d3
      .geoMercator()
      .center([-117.172087, 32.8437])
      .scale(90000)
      .translate([width / 2, height / 2]);

    d3.csv(artFile).then(function (data) {
      art_data = data;

      artMarkers = svg
        .selectAll("circle")
        .data(data)
        .enter()
        .append("circle")
        .attr("cx", function (d) {
          return projection([+d.lng, +d.lat])[0];
        })
        .attr("cy", function (d) {
          return projection([+d.lng, +d.lat])[1];
        })
        .attr("r", max_r)
        .style("fill", function (d) {
          let status_split = d["status"].split(" ");
          let status = status_split[3].concat(" ", status_split[4]);
          return colorScale(status);
        });

      document.getElementById("slider").addEventListener("input", function () {
        year = +this.value;
        updateMarkers();
      });

      document
        .getElementById("yearDisplay")
        .addEventListener("input", function () {
          year = +this.value;
          document.getElementById("slider").value = year;
          updateMarkers();
        });

      document
        .getElementById("artistSelector")
        .addEventListener("change", function () {
          artist = this.value;
          updateMarkers();
        });

      window.addEventListener("resize", resizeMarkers);

      updateMarkers();
    });
  });
</script>

<div id="map"></div>
<main>
  <div class="title">
    <center>
      <h1>Painting a Picture: The Growth of Public Art in San Diego</h1>
      <p>Currently showing locations of art made before {year}</p>
    </center>
  </div>
  <div class="overlay">
    <label for="yearDisplay"
      >Scrub or type a year to see how public art has spread over time:</label
    >
    <br />
    <center>
      <input id="yearDisplay" type="number" bind:value={year} />
    </center>
    <input id="slider" type="range" min="1930" max="2022" bind:value={year} />
    <br /><br />
    <label for="artist" color="#deadff">Artist to display:</label>
    <br />
    <select id="artistSelector" name="artist" bind:value={artist}>
      <option value="all">All Artists</option>
      <option value="Alvaro Blancarte"> Alvaro Blancarte </option>
      <option value="Angelo Camporaso"> Angelo Camporaso </option>

    </select>
  </div>
  <div class="legend">
    <center>
      <h3>Collection Status</h3>
    </center>
    <ul>
      <li>🟩 - On Display</li>
      <li class="label">🟥 - By Appointment Only</li>
      <li class="label">🟦 - Public Tours Available</li>
    </ul>
  </div>
</main>

<style>
  #map {
    width: 100vw;
    height: 100vh;
    padding: 0px;
  }
  .title {
    font-size: 1vw;
    background-color: rgba(255, 255, 255, 0.7);
    position: absolute;
    width: 100vw;
    top: 0px;
    z-index: 3;
    font-family: Arial, Helvetica, sans-serif;
  }
  .overlay {
    font-size: 0.5vw;
    background-color: rgba(255, 255, 255, 0.7);
    position: absolute;
    min-width: 10vw;
    height: 100vh;
    width: 15%;
    top: 2vw;
    bottom: 10px;
    right: 10px;
    padding: 15px;
    z-index: 3;
    border-radius: 10px;
  }
  .legend {
    font-size: 1vw;
    background-color: rgb(255, 255, 255, 0.7);
    position: absolute;
    min-width: 2vw;
    width: 15%;
    bottom: 10px;
    left: 2vw;;
    padding: 10px;
    z-index: 3;
    font-family: Arial, Helvetica, sans-serif;
    border-radius: 10px;
  }
  .legend h3 {
    font-size: 1.8vw;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  label {
    font-size: 1vw;
    font-family: sans-serif;
    font-weight: bold;
    color: black;
  }
  input[type="number"] {
    text-align: center;
    background: none;
    border: none;
    outline: none;
    font-size: 2vw;
    font-weight: bold;
    color: black;
    width: 100px;
    padding: 10px;
    transition-duration: 200ms;
  }
  input[type="number"]:focus {
    outline: none;
    background-color: lightgray;
    border-color: #007bff;
    box-shadow: 0 0 5px goldenrod;
    transition-duration: 200ms;
  }

  input,
  select {
    width: 15vw;
    height: 2vw;;
    font-size: 1vw;
    position: relative;
  }
</style>