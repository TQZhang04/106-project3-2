<script>
  import { onMount } from "svelte";
  import mapboxgl from "mapbox-gl";
  import * as d3 from "d3";

  const MAPBOX_ACCESS_TOKEN =
    "pk.eyJ1IjoidHF6aGFuZzA0IiwiYSI6ImNsdnl1OTRjNzJ2cTAyanBnaHB1cG1hODcifQ.AbbWEKcCU1cc2kfGEP6GPQ";

  onMount(() => {
    mapboxgl.accessToken = MAPBOX_ACCESS_TOKEN;

    const map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/dark-v11",
      center: [-117.1611, 32.7157],
      zoom: 9.96,
      minZoom: 9,
      maxZoom: 13,
    });

    const marker_container = d3
      .select(map.getCanvasContainer())
      .append("svg")
      .attr("width", "100%")
      .attr("height", "100%")
      .style("position", "absolute")
      .style("z-index", 2);

    d3.csv(artFile).then(function (d) {
      art_data = d;
      console.log(art_data);
      create_art_markers(d);
      console.log(art_markers);
    });

    function create_art_markers(art_data) {
      art_markers = marker_container
        .selectAll("circle")
        .data(art_data)
        .enter()
        .append("circle")
        .attr("r", default_r)
        .style("fill", "#deadff")
        .attr("stroke-width", "1")
        .attr("fill-opacity", "0.4");
      position_art_markers();
    }

    function position_art_markers() {
      art_markers
        .attr("cx", function (d) {
          return project(d).x;
        })
        .attr("cy", function (d) {
          return project(d).y;
        });
    }

    function project(d) {
      return map.project(
        new mapboxgl.LngLat(parseFloat(d.lng), parseFloat(d.lat))
      );
    }

    map.on("viewreset", position_art_markers);
    map.on("move", position_art_markers);
    map.on("moveend", position_art_markers);
  });

  function update_art_markers() {
    art_markers
      .transition()
      .duration(200)
      .attr("r", function (d) {
        let art_year = d.accession_number.slice(0, 4);
        let artist_name = d.artist;
        let artist_group = artist_name.includes("(");
        if (artist_group) {
          artist_name = artist_name.slice(0, artist_name.indexOf("("));
        }
        let artist_list = artist_name.split("|");
        for (let i = 0; i < artist_list.length; i++) {
          artist_list[i] = artist_list[i].trim();
        }
        if (art_year <= year) {
          if (artist == "all" || artist_list.includes(artist)) {
            return 5;
          }
        }
        return 0;
      });
  }

  const artFile =
    "https://raw.githubusercontent.com/TQZhang04/106-project3/main/public_art_locations_datasd.csv";
  let art_data = [];
  let art_color = ["#20fc03", "#f4fc03", "#fca103", "#03b1fc", "#fc0303"];
  let art_markers;
  const default_r = 5;
  let year = 1950;
  let artist = "all";

  $: {
    console.log(year);
    // if (art_data.length !== 0) {
    //   update_art_markers();
    // }
    console.log(artist);
    // console.log(art_data);
    // console.log(art_markers);
  }
</script>

<main>
  <div id="map">
    <div class="title">
      <center>
        <h1>Painting a Picture: The Growth of Public Art in San Diego</h1>
        <p>Currently showing locations of art from before {year}</p>
      </center>
    </div>
    <div class="overlay">
      <label for="yearDisplay">Artwork made before:</label>
      <center>
        <input id="yearDisplay" type="number" bind:value={year} />
      </center>
      <input id="slider" type="range" min="1950" max="2022" bind:value={year} />
      <label for="artist" color="#deadff">Artist to display:</label>
      <select id="artist" name="artist" bind:value={artist}>
        <option value="all">All Artists</option>
        <option value="Alvaro Blancarte"> Alvaro Blancarte </option>
        <option value="Angelo Camporaso"> Angelo Camporaso </option>
        <option value="Bernard Bobitch"> Bernard Bobitch </option>
        <option value="Christine Oatman"> Christine Oatman </option>
        <option value="Chuck Moffit"> Chuck Moffit </option>
        <option value="Daniel Renner"> Daniel Renner </option>
        <option value="Einar de la Torre"> Einar de la Torre </option>
        <option value="Fe McQueen"> Fe McQueen </option>
        <option value="Gwendolyn Gomez"> Gwendolyn Gomez </option>
        <option value="James Brown"> James Brown </option>
        <option value="James Nelson"> James Nelson </option>
        <option value="James Nelson"> James Nelson </option>
        <option value="Jan Phillips"> Jan Phillips </option>
        <option value="Jerrilynn Bouguereau"> Jerrilynn Bouguereau </option>
        <option value="Joseph Waters"> Joseph Waters </option>
        <option value="Machi Uchida"> Machi Uchida </option>
        <option value="Maidie Morris"> Maidie Morris </option>
        <!-- <option value="Marcos Ramirez "ERRE""> Marcos Ramirez "ERRE" </option> -->
        <option value="Marisol Rendón"> Marisol Rendón </option>
        <option value="Mary Lynn Dominguez"> Mary Lynn Dominguez </option>
        <option value="Matthew Welsh"> Matthew Welsh </option>
        <option value="Reanne Estrada"> Reanne Estrada </option>
        <option value="Roberto Salas"> Roberto Salas </option>
        <option value="Roberto Salas"> Roberto Salas </option>
        <option value="Robin Brailsford"> Robin Brailsford </option>
        <option value="Rosemary Boost"> Rosemary Boost </option>
        <option value="T.J. Dixon"> T.J. Dixon </option>
        <option value="Wallace Roberts and Todd, LLC">
          Wallace Roberts and Todd, LLC
        </option>
        <option value="Walter Cotten"> Walter Cotten </option>
        <option value="Wick Alexander"> Wick Alexander </option>
        <option value="William Hogarth"> William Hogarth </option>
        <option value="Ysela Chacon"> Ysela Chacon </option>
        <option value="Actual Size Artworks"> Actual Size Artworks </option>
        <option value="Alber De Matteis"> Alber De Matteis </option>
        <option value="Alber De Matteis"> Alber De Matteis </option>
        <option value="Alexander Finta"> Alexander Finta </option>
        <option value="Alexander Kohnke"> Alexander Kohnke </option>
        <option value="Aleya Lanteigne"> Aleya Lanteigne </option>
        <option value="Alfred Mitchell"> Alfred Mitchell </option>
        <option value="Alida Cervantes"> Alida Cervantes </option>
        <option value="Allison Wiese"> Allison Wiese </option>
        <option value="Amanda Kachadoorian"> Amanda Kachadoorian </option>
        <option value="Amy Ennis Achaibou"> Amy Ennis Achaibou </option>
        <option value="Amy Levine"> Amy Levine </option>
        <option value="Ando Hiroshige"> Ando Hiroshige </option>
        <option value="Andrea Chung"> Andrea Chung </option>
        <option value="Andrew Alcasid"> Andrew Alcasid </option>
        <option value="Angie Jennings"> Angie Jennings </option>
        <option value="Anna Hyatt Huntington"> Anna Hyatt Huntington </option>
        <option value="Annaliese Cassarino"> Annaliese Cassarino </option>
        <option value="Anne Mudge"> Anne Mudge </option>
        <option value="Barbara Grygutis"> Barbara Grygutis </option>
        <option value="Barney Reid"> Barney Reid </option>
        <option value="Beliz Iristay"> Beliz Iristay </option>
        <option value="Belle Goldschlager Baranceanu">
          Belle Goldschlager Baranceanu
        </option>
        <option value="Bernar Venet"> Bernar Venet </option>
        <option value="Beth King"> Beth King </option>
        <option value="Bhavna Mehta"> Bhavna Mehta </option>
        <option value="Bill Mosley"> Bill Mosley </option>
        <option value="Binh Danh"> Binh Danh </option>
        <option value="Blue McRight"> Blue McRight </option>
        <option value="Bob Gromofsky"> Bob Gromofsky </option>
        <option value="Bob Matheny"> Bob Matheny </option>
        <option value="Bob Nugent"> Bob Nugent </option>
        <option value="Bobbi Lewis"> Bobbi Lewis </option>
        <option value="Brad Maxey"> Brad Maxey </option>
        <option value="Brianna Rigg"> Brianna Rigg </option>
        <option value="Bruno Mankowski"> Bruno Mankowski </option>
        <option value="Byron Goto"> Byron Goto </option>
        <option value="C. Ree"> C. Ree </option>
        <option value="Carlos Castro"> Carlos Castro </option>
        <option value="Cat Chiu Phillips"> Cat Chiu Phillips </option>
        <option value="Cecil King"> Cecil King </option>
        <option value="Chantal Wnuk"> Chantal Wnuk </option>
        <option value="Charles Arthur Fries"> Charles Arthur Fries </option>
        <option value="Charles Reiffel"> Charles Reiffel </option>
        <option value="Charles Ross"> Charles Ross </option>
        <option value="Charles Snowden"> Charles Snowden </option>
        <option value="Cheryl Parry"> Cheryl Parry </option>
        <option value="Chitra Gopalakrishnan"> Chitra Gopalakrishnan </option>
        <option value="Chong X. Thao"> Chong X. Thao </option>
        <option value="Christian Garcia-Olivo"> Christian Garcia-Olivo </option>
        <option value="Christine Oatman"> Christine Oatman </option>
        <option value="Christopher Hobbs"> Christopher Hobbs </option>
        <option value="Christopher Lee"> Christopher Lee </option>
        <option value="Christopher Puzio"> Christopher Puzio </option>
        <option value="Claudia Cano"> Claudia Cano </option>
        <option value="Cognate Collective"> Cognate Collective </option>
        <option value="Constance Todd"> Constance Todd </option>
        <option value="Dan Dickey"> Dan Dickey </option>
        <option value="Dana Montlack"> Dana Montlack </option>
        <option value="Daniel Brice"> Daniel Brice </option>
        <option value="Danielle Dean"> Danielle Dean </option>
        <option value="David Baze"> David Baze </option>
        <option value="David White"> David White </option>
        <option value="Deanne Sabeck"> Deanne Sabeck </option>
        <option value="Debby and Larry Kline"> Debby and Larry Kline </option>
        <option value="Deladier Almeida"> Deladier Almeida </option>
        <option value="Don Dudley"> Don Dudley </option>
        <option value="Donal Hord"> Donal Hord </option>
        <option value="Donald Borthwick"> Donald Borthwick </option>
        <option value="Donald Lipski"> Donald Lipski </option>
        <option value="Donna Leavitt"> Donna Leavitt </option>
        <option value="Doris Bittar"> Doris Bittar </option>
        <option value="Doron Rosenthal"> Doron Rosenthal </option>
        <option value="Dudley Kendall"> Dudley Kendall </option>
        <option value="Duke Windsor"> Duke Windsor </option>
        <option value="Ed Dwight"> Ed Dwight </option>
        <option value="Eddie L. Edwards"> Eddie L. Edwards </option>
        <option value="Edgar Hatten"> Edgar Hatten </option>
        <option value="Edward James Fraughton"> Edward James Fraughton </option>
        <option value="Ellen Phillips"> Ellen Phillips </option>
        <option value="Ellen Phillips"> Ellen Phillips </option>
        <option value="Elliot Bouton Torrey"> Elliot Bouton Torrey </option>
        <option value="Eric Blau"> Eric Blau </option>
        <option value="Ernest Silva"> Ernest Silva </option>
        <option value="Ernesto E. Tamariz"> Ernesto E. Tamariz </option>
        <option value="Eugene Taylor"> Eugene Taylor </option>
        <option value="Eva Struble"> Eva Struble </option>
        <option value="Faiya Fredman"> Faiya Fredman </option>
        <option value="Farshid Bazmandegan"> Farshid Bazmandegan </option>
        <option value="Felix Achilles Peano"> Felix Achilles Peano </option>
        <option value="Francesco Bartolozzi"> Francesco Bartolozzi </option>
        <option value="Francis Livingston"> Francis Livingston </option>
        <option value="Francisco Eme"> Francisco Eme </option>
        <option value="Frank Damiano"> Frank Damiano </option>
        <option value="Frank Jones"> Frank Jones </option>
        <option value="Frank Oriti"> Frank Oriti </option>
        <option value="Fred Cooper"> Fred Cooper </option>
        <option value="Fred Holle"> Fred Holle </option>
        <option value="Frederick William Schweigardt">
          Frederick William Schweigardt
        </option>
        <option value="Frederick Yates"> Frederick Yates </option>
        <option value="Furio Piccirilli"> Furio Piccirilli </option>
        <option value="Gail Roberts"> Gail Roberts </option>
        <option value="Gary Hill"> Gary Hill </option>
        <option value="Geoffrey Krueger"> Geoffrey Krueger </option>
        <option value="George Blais"> George Blais </option>
        <option value="George Lewis"> George Lewis </option>
        <option value="Gerrit Greve"> Gerrit Greve </option>
        <option value="Gladys Nilsson"> Gladys Nilsson </option>
        <option value="Glenn Ness"> Glenn Ness </option>
        <option value="Griselda Rosas"> Griselda Rosas </option>
        <option value="Guy Williams"> Guy Williams </option>
        <option value="Harold Gregor"> Harold Gregor </option>
        <option value="Helmut Löhr"> Helmut Löhr </option>
        <option value="Herbert Adams"> Herbert Adams </option>
        <option value="Herbert Olds"> Herbert Olds </option>
        <option value="Hilda Alice Avery Inch"> Hilda Alice Avery Inch </option>
        <option value="Holly Weston"> Holly Weston </option>
        <option value="Huai Li"> Huai Li </option>
        <option value="Hugo Ballin"> Hugo Ballin </option>
        <option value="Hugo Crosthwaite"> Hugo Crosthwaite </option>
        <option value="Ilse Ruocco"> Ilse Ruocco </option>
        <option value="Ingram Ober"> Ingram Ober </option>
        <option value="Ingram Ober"> Ingram Ober </option>
        <option value="Irving J. Gill"> Irving J. Gill </option>
        <option value="Italo Scanga"> Italo Scanga </option>
        <option value="Ivan Messenger"> Ivan Messenger </option>
        <option value="Jackson Woolley"> Jackson Woolley </option>
        <option value="Jackson Woolley and Ellamarie Woolley">
          Jackson Woolley and Ellamarie Woolley
        </option>
        <option value="Jacob A. Pfeiffer"> Jacob A. Pfeiffer </option>
        <option value="James Pennuto"> James Pennuto </option>
        <option value="James Skalman"> James Skalman </option>
        <option value="James Tank Porter"> James Tank Porter </option>
        <option value="James Wilsterman"> James Wilsterman </option>
        <option value="Jamex de la Torre"> Jamex de la Torre </option>
        <option value="Janelle Iglesias"> Janelle Iglesias </option>
        <option value="Janet Cooling"> Janet Cooling </option>
        <option value="Janet Zweig"> Janet Zweig </option>
        <option value="Jason Sherry"> Jason Sherry </option>
        <option value="Jay Johnson"> Jay Johnson </option>
        <option value="Jay McCafferty"> Jay McCafferty </option>
        <option value="Jean Cornwell Wheat"> Jean Cornwell Wheat </option>
        <option value="Jean Lowe"> Jean Lowe </option>
        <option value="Jean Swiggett"> Jean Swiggett </option>
        <option value="Jeri Deneen"> Jeri Deneen </option>
        <option value="Jessica McCambly"> Jessica McCambly </option>
        <option value="Jesus Dominguez"> Jesus Dominguez </option>
        <option value="Jihmye Collins"> Jihmye Collins </option>
        <option value="Jill Moon"> Jill Moon </option>
        <option value="Joanne Hayakawa"> Joanne Hayakawa </option>
        <option value="Joe Yorty"> Joe Yorty </option>
        <option value="Joel Sotelo"> Joel Sotelo </option>
        <option value="John Brinton Hogan"> John Brinton Hogan </option>
        <option value="John Chang"> John Chang </option>
        <option value="John Charles Dollman"> John Charles Dollman </option>
        <option value="John Dee"> John Dee </option>
        <option value="John Halaka"> John Halaka </option>
        <option value="John Krimmel"> John Krimmel </option>
        <option value="John Mireles"> John Mireles </option>
        <option value="John Nolen"> John Nolen </option>
        <option value="John Oliver Lewis"> John Oliver Lewis </option>
        <option value="John Reth"> John Reth </option>
        <option value="John Rogers"> John Rogers </option>
        <option value="Jose Moya del Pino"> Jose Moya del Pino </option>
        <option value="Joseph Haynes"> Joseph Haynes </option>
        <option value="Joshua Eggleton"> Joshua Eggleton </option>
        <option value="Joshua Tonies"> Joshua Tonies </option>
        <option value="Joyce Cutler Shaw"> Joyce Cutler Shaw </option>
        <option value="Juan B. Larrinaga"> Juan B. Larrinaga </option>
        <option value="Juan Carlos Quintana"> Juan Carlos Quintana </option>
        <option value="Juan Miguel Cabrera"> Juan Miguel Cabrera </option>
        <option value="Jules Chéret"> Jules Chéret </option>
        <option value="Julie Haskell Oleson"> Julie Haskell Oleson </option>
        <option value="Kaori Fukuyama"> Kaori Fukuyama </option>
        <option value="Katie Ruiz"> Katie Ruiz </option>
        <option value="Kelsey Brookes"> Kelsey Brookes </option>
        <option value="Kim Emerson"> Kim Emerson </option>
        <option value="Kim Rugg"> Kim Rugg </option>
        <option value="Kimberly Heard"> Kimberly Heard </option>
        <option value="Kirk Crow"> Kirk Crow </option>
        <option value="Kline Swonger"> Kline Swonger </option>
        <option value="Kristin Nason"> Kristin Nason </option>
        <option value="Larry Kirkland"> Larry Kirkland </option>
        <option value="Laszlo Pal Kiss"> Laszlo Pal Kiss </option>
        <option value="Leslie Ryan"> Leslie Ryan </option>
        <option value="Leslie William Lee"> Leslie William Lee </option>
        <option value="Lew Achen"> Lew Achen </option>
        <option value="Lilia Peji"> Lilia Peji </option>
        <option value="Lisa Hutton"> Lisa Hutton </option>
        <option value="Living Lenses"> Living Lenses </option>
        <option value="Lorena Gómez Mostajo"> Lorena Gómez Mostajo </option>
        <option value="Lydia Knapp Horton"> Lydia Knapp Horton </option>
        <option value="Lynn Schuette"> Lynn Schuette </option>
        <option value="Lynn Susholtz"> Lynn Susholtz </option>
        <option value="Lynn Susholtz"> Lynn Susholtz </option>
        <option value="MR Barnadas"> MR Barnadas </option>
        <option value="Madeline Wiener"> Madeline Wiener </option>
        <option value="Malcolm Jones"> Malcolm Jones </option>
        <option value="Malcolm Leland"> Malcolm Leland </option>
        <option value="Manuelita Brown"> Manuelita Brown </option>
        <option value="Margaret Noble"> Margaret Noble </option>
        <option value="Marianela de la Hoz"> Marianela de la Hoz </option>
        <option value="Mario Lara"> Mario Lara </option>
        <option value="Mario Torero"> Mario Torero </option>
        <option value="Marisol Rendón"> Marisol Rendón </option>
        <option value="Marjorie Taylor"> Marjorie Taylor </option>
        <option value="Marta Palau"> Marta Palau </option>
        <option value="Martha Alf"> Martha Alf </option>
        <option value="Mary Belle Williams"> Mary Belle Williams </option>
        <option value="Mary Buckman"> Mary Buckman </option>
        <option value="Mary Lynn Dominguez"> Mary Lynn Dominguez </option>
        <option value="Matt Rich"> Matt Rich </option>
        <option value="Matthew Hebert"> Matthew Hebert </option>
        <option value="Maurice Braun"> Maurice Braun </option>
        <option value="Maya VanderSchuit"> Maya VanderSchuit </option>
        <option value="Melissa Walter"> Melissa Walter </option>
        <option value="Merryl Berner Cicourel"> Merryl Berner Cicourel </option>
        <option value="Michael Chapman"> Michael Chapman </option>
        <option value="Michael Merck"> Michael Merck </option>
        <option value="Michael Mulno"> Michael Mulno </option>
        <option value="Michael Wheelden"> Michael Wheelden </option>
        <option value="Michelle Moore"> Michelle Moore </option>
        <option value="Miguel Salmeron"> Miguel Salmeron </option>
        <option value="Mildred Bryant Brooks"> Mildred Bryant Brooks </option>
        <option value="Millard Owen Sheets"> Millard Owen Sheets </option>
        <option value="Molly Hunter Umholtz"> Molly Hunter Umholtz </option>
        <option value="Nassem Navab-Gojrati"> Nassem Navab-Gojrati </option>
        <option value="Nikko Mueller"> Nikko Mueller </option>
        <option value="Nina Karavasiles"> Nina Karavasiles </option>
        <option value="Nina Montejano"> Nina Montejano </option>
        <option value="Nobuho Nagasawa"> Nobuho Nagasawa </option>
        <option value="Norman Lundin"> Norman Lundin </option>
        <option value="Omar Lopex"> Omar Lopex </option>
        <option value="Otto Schneider"> Otto Schneider </option>
        <option value="Paul Ecke"> Paul Ecke </option>
        <option value="Paul Hobson"> Paul Hobson </option>
        <option value="Perry Vasquez"> Perry Vasquez </option>
        <option value="Peter Brian Daly"> Peter Brian Daly </option>
        <option value="Philip Matzigkeit"> Philip Matzigkeit </option>
        <option value="Philip Matzigkeit"> Philip Matzigkeit </option>
        <option value="Philipp Scholz Rittermann">
          Philipp Scholz Rittermann
        </option>
        <option value="Quincy Troupe"> Quincy Troupe </option>
        <option value="Rafael Lopez"> Rafael Lopez </option>
        <option value="Raul Espinoza"> Raul Espinoza </option>
        <option value="Raul Guerrero"> Raul Guerrero </option>
        <option value="Raul Trejo"> Raul Trejo </option>
        <option value="Rebecca Webb"> Rebecca Webb </option>
        <option value="Reinhart Revilla Selvik">
          Reinhart Revilla Selvik
        </option>
        <option value="Richard Allen Morris"> Richard Allen Morris </option>
        <option value="Richard John Croft"> Richard John Croft </option>
        <option value="Richard Keely"> Richard Keely </option>
        <option value="Richard Spaulding"> Richard Spaulding </option>
        <option value="Richard Turner"> Richard Turner </option>
        <option value="Richard Yale"> Richard Yale </option>
        <option value="Robert Goldman"> Robert Goldman </option>
        <option value="Robert Katsusuke Ogata"> Robert Katsusuke Ogata </option>
        <option value="Robert Kelly"> Robert Kelly </option>
        <option value="Roberto Delgado"> Roberto Delgado </option>
        <option value="Roberto Salas"> Roberto Salas </option>
        <option value="Robin Brailsford"> Robin Brailsford </option>
        <option value="Robin Brailsford"> Robin Brailsford </option>
        <option value="Robin Bright"> Robin Bright </option>
        <option value="Roman de Salvo"> Roman de Salvo </option>
        <option value="Roman de Salvo"> Roman de Salvo </option>
        <option value="Rose Hanks"> Rose Hanks </option>
        <option value="Russell Baldwin"> Russell Baldwin </option>
        <option value="Ruth Hayward"> Ruth Hayward </option>
        <option value="Ruth Wallen"> Ruth Wallen </option>
        <option value="Sally Storch"> Sally Storch </option>
        <option value="Sarah Lejeune"> Sarah Lejeune </option>
        <option value="Sarah Stangeland"> Sarah Stangeland </option>
        <option value="Scott Polach"> Scott Polach </option>
        <option value="Sean Carter"> Sean Carter </option>
        <option value="Sheldon Kirby"> Sheldon Kirby </option>
        <option value="Shinpei Takeda"> Shinpei Takeda </option>
        <option value="Sofia V. Gonzalez"> Sofia V. Gonzalez </option>
        <option value="Stephanie and Ken Goldman">
          Stephanie and Ken Goldman
        </option>
        <option value="Stephen Milner"> Stephen Milner </option>
        <option value="Steven Pollack"> Steven Pollack </option>
        <option value="Stone Paper Scissors"> Stone Paper Scissors </option>
        <option value="Susan Chorpenning"> Susan Chorpenning </option>
        <option value="Susan Zoccola"> Susan Zoccola </option>
        <option value="T.J. Dixon"> T.J. Dixon </option>
        <option value="Tatiana Ortiz-Rubio"> Tatiana Ortiz-Rubio </option>
        <option value="Teddy Cruz"> Teddy Cruz </option>
        <option value="Terri Hughes-Oelrich"> Terri Hughes-Oelrich </option>
        <option value="Thomas Cook"> Thomas Cook </option>
        <option value="Thomas Driscoll"> Thomas Driscoll </option>
        <option value="Timothy Murdoch"> Timothy Murdoch </option>
        <option value="Tom Marioni"> Tom Marioni </option>
        <option value="Toru Nakatani"> Toru Nakatani </option>
        <option value="Trevor Amery"> Trevor Amery </option>
        <option value="Unknown"> Unknown </option>
        <option value="Vallo Riberto"> Vallo Riberto </option>
        <option value="Victoria Fu"> Victoria Fu </option>
        <option value="Virginia Lee"> Virginia Lee </option>
        <option value="Wade Cline"> Wade Cline </option>
        <option value="Walter Haase Wojtyla"> Walter Haase Wojtyla </option>
        <option value="Wendell Kling"> Wendell Kling </option>
        <option value="Wendy Maruyama"> Wendy Maruyama </option>
        <option value="Wick Alexander"> Wick Alexander </option>
        <option value="Willard F. Capps"> Willard F. Capps </option>
        <option value="William Blake"> William Blake </option>
        <option value="William Feeney"> William Feeney </option>
        <option value="William Gambini"> William Gambini </option>
        <option value="William Hogarth"> William Hogarth </option>
        <option value="Xavier Martinez"> Xavier Martinez </option>
        <option value="Yasmine Kasem"> Yasmine Kasem </option>
        <option value="Yoram Wolberger"> Yoram Wolberger </option>
        <option value="Ysela Chacon"> Ysela Chacon </option>
        <option value="possibly R.E. Henderson">
          possibly R.E. Henderson
        </option>
        <option value="scott b. davis"> scott b. davis </option>
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
  </div>
</main>

<style>
  #map {
    width: 100vw;
    height: 100vh;
    padding: 0px;
  }
  body {
    margin: 0;
    padding: 0;
  }

  #map {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
    height: 100%;
  }

  input {
    display: inline-block;
    width: 100%;
    position: relative;
    margin: 0;
    cursor: pointer;
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
    float: left;
  }
</style>
