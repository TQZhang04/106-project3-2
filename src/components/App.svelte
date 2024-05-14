<script>
  import { onMount } from "svelte";
  import * as d3 from "d3";

  let year = 1930;
  let artist = "all";
  let art_data = [];
  let art_color = ["#20fc03", "#f4fc03", "#fca103", "#03b1fc", "#fc0303"];
  const artFile = "https://raw.githubusercontent.com/TQZhang04/106-project3/main/public_art_locations_datasd.csv";

  onMount(() => {
    const width = document.getElementById('map').clientWidth;
    const height = document.getElementById('map').clientHeight;
    const svg = d3.select("#map")
                  .append("svg")
                  .attr("width", width)
                  .attr("height", height);

    // Load background map image
    svg.append("image")
       .attr("xlink:href", "https://raw.githubusercontent.com/TQZhang04/106-project3-2/main/San%20Diego.png")
       .attr("width", width)
       .attr("height", height);

    const projection = d3.geoMercator()
                         .center([-117.161087, 32.7157]) // Adjust center to match image map
                         .scale(50000)
                         .translate([width / 2, height / 2]);

    d3.csv(artFile).then(function(data) {
      art_data = data;

      const artMarkers = svg.selectAll("circle")
                            .data(data)
                            .enter()
                            .append("circle")
                            .attr("cx", function(d) { return projection([+d.lng, +d.lat])[0]; })
                            .attr("cy", function(d) { return projection([+d.lng, +d.lat])[1]; })
                            .attr("r", 5)
                            .style("fill", function(d) {
                              if (d["status"] == "Collection Status : On View") {
                                return art_color[0];
                              }
                              if (d["status"] == "Collection Status : Installation pending") {
                                return art_color[1];
                              }
                              if (d["status"] == "Collection Status : Temporary Installation") {
                                return art_color[2];
                              }
                              if (d["status"] == "Collection Status : Public Tours Available / Contact the Commission for Arts and Culture") {
                                return art_color[3];
                              }
                              if (d["status"] == "Collection Status : By Appointment Only / Contact the Hervey Family Rare Book Room") {
                                return art_color[4];
                              }
                              if (d["status"] == "Collection Status : By Appointment Only / Contact the Commission for Arts and Culture") {
                                return art_color[4];
                              }
                            });

      function updateMarkers() {
        artMarkers.attr("r", function(d) {
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
              return 5;
            }
          }
          return 0;
        });
      }

      document.getElementById('slider').addEventListener('input', function() {
        year = +this.value;
        updateMarkers();
      });

      document.getElementById('yearDisplay').addEventListener('input', function() {
        year = +this.value;
        document.getElementById('slider').value = year;
        updateMarkers();
      });
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
    <label for="yearDisplay">Scrub or type a year to see how public art displays have spread over time:</label>
    <center>
      <input id="yearDisplay" type="number" bind:value={year} />
    </center>
    <input id="slider" type="range" min="1930" max="2022" bind:value={year} />
    <label for="artist" color="#deadff">Artist to display:</label>
    <select id="artist" name="artist" bind:value={artist}>
      <option value="all">All Artists</option>
      <!-- Add more artist options here -->
    </select>
  </div>
  <div class="legend">
    <center>
      <h3>Collection Status</h3>
    </center>
    <ul>
      <li>🟩 - On View</li>
      <li class="label">🟨 - Installation pending</li>
      <li class="label">🟧 - Temporary Installation</li>
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
    font-size: 1em;
    background-color: rgba(255, 255, 255, 0.7);
    position: absolute;
    min-width: 250px;
    left: 0px;
    right: 0px;
    top: 0px;
    z-index: 3;
    font-family: Arial, Helvetica, sans-serif;
  }
  .overlay {
    font-size: 0.5em;
    background-color: rgba(255, 255, 255, 0.7);
    position: absolute;
    min-width: 250px;
    width: 15%;
    top: 130px;
    bottom: 10px;
    right: 10px;
    padding: 15px;
    z-index: 3;
    border-radius: 10px;
  }
  .legend {
    font-size: 1em;
    background-color: rgb(255, 255, 255, 0.7);
    position: absolute;
    min-width: 150px;
    width: 15%;
    bottom: 10px;
    left: 10px;
    padding: 10px;
    z-index: 3;
    font-family: Arial, Helvetica, sans-serif;
    border-radius: 10px;
  }
  .legend h3 {
    font-size: 20px;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  label {
    font-size: 2em;
    font-family: sans-serif;
    font-weight: bold;
    color: black;
    float: left;
  }
  input[type="number"] {
    text-align: center;
    background: none;
    border: none;
    outline: none;
    font-size: 20px;
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
</style>
