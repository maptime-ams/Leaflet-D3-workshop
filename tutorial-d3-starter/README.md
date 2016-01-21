# D3
## grafieken en kaarten maken met d3
is een open javascript bibliotheek. D3 staat voor Data Driven Documents. De data zoek je zelf. De documents zijn web-based (HTML en SVG) en D3 maakt de verbinding tussen de data en de documents(‘drivin’). D3js. is ontwikkeld door Mike Bostock en is volledig open source en vrij beschikbaar op GitHub. Je kunt er prachtige, ook interactieve visualisatie, grafieken van data maken, maar natuurlijk ook een kaart.

* Op de D3 website vind je prachtige voorbeelden, laat je hier vooral inspireren en gebruik de code! https://github.com/mbostock/d3/wiki/Gallery

### wat heb je nodig
* basiskennis van HTML, DOM, CSS en JavaScript is handig. meer uitleg vind je hier: http://alignedleft.com/tutorials/d3/fundamentals
* browser, chrome, heeft een fijne debugger. 

### De eerste kaart
1. open je teksteditor 
2. start met een basis HTML pagina
3. sla deze op in een nieuwe folder en noem deze index.html

	~~~~
	<!doctype html>
	<html lang="nl">
	
	<html>
	 	<head>
			<meta charset="utf-8">
	    		<title>basis HTML</title>  		 
	 	</head>
	       
	 	<body>
	     		<H1>voorbeeld</H1>
	 	</body>
	</html>
	~~~~

4. Ga naar d3js.org. Scroll naar beneden en kopieer de nieuwste release. Aangezien we al utf-8 los in de  head hebben staan, hoeft het niet meer in het script. (utf-8 zorgt ervoor dat alle dakjes en accenten goed komen te staan.)

	~~~~
	<script src="http://d3js.org/d3.v3.min.js"></script>
	~~~~

	* De js bibliotheken, zie je vaak in de head staan. Het is beter om deze zover mogelijk onderaan in de body te plaatsen. Dit scheelt namelijk snelheid bij het laden.


5. Open een nieuw bestand. Noem bestand bijvoorbeeld ‘main.css’ en plaats het in een nieuwe map: ‘style’ in dezelfde folder als je index.html. 
6. Je kunt ook gelijk mappen aanmaken voor: ‘images’ en ‘js’. 
Open je ‘index.html’ bestand en zet de link naar je css bestand in je head

	~~~~
	<link rel="stylesheet" href="style/main.css"/>
	~~~~

7. verander de titel in bijv. “mijn eerste kaart met d3”. je pagina ziet er als volgt uit.

	~~~~
	<!doctype html>
	<html lang="nl">
	<html>
	 	<head>
			<meta charset=“utf-8">
	    	<title>Mijn eerste kaart met D3</title> 
			<link rel="stylesheet" href=“style/main.css”/>		</head>   
	 	<body>
	     	<H1>voorbeeld</H1>
			<script src=“//d3js.org/d3.v3.min.js"></script> 
			<script> hier komt je code </script>
	 	</body>
	</html>
	~~~~


8. Vervang “hier komt je code” met het volgende

	~~~~
		<script> 
			//Width and height
			var w = 500;
			var h = 300;

			//Define map projection
			var projection = d3.geo.mercator()
						.center([ 30, 40 ])
						.translate([ w/2, h/2 ])
						.scale([ w/4 ]);

			//Define path generator
			var path = d3.geo.path()
						.projection(projection);

			//Create SVG
			var svg = d3.select("body")
						.append("svg")
						.attr("width", w)
						.attr("height", h);
			</script>
	~~~~

9. Wat heb je gedaan?
	* // de regel achter de slashes is het commentaar/verduidelijking. Dit geeft de stappen weer wat je aan het doen bent.
	* ; altijd belangrijk geeft het einde van dit stukje code.
	* Als eerste heb je de width en height gedefineerd. 
	* var staat voor variabele. 
	
	~~~~
	//Width and height
		var w = 500;
		var h = 300;
	~~~~
	
 	* vervolgens kiezen we de projectie van de kaart. Mercator is de meest gebruikte
 	* center gaat met longitude en latitude. 
 	* translate zorgt op deze manier ervoor, dat de kaart in het midden komt
	* schaal zegt iets over het zoomniveau.  

	~~~~
	//Define map projection
	 	var projection = d3.geo.mercator()
			 	.center([30, 40])
				.translate([ w/2, h/2 ])
				.scale([ w/7 ]);
	~~~~
				
	* Zodra de variable projection gemaakt is kunnen we de geografische data omzetten naar SVG dmv path 
	
	~~~~
	//Define path generator
		var path = d3.geo.path()
				.projection(projection);
	~~~~			

 	* Vervolgens maken we het ‘canvas’ waarin we de kaart tonen. Je maakt een variabele, die noem je bijv. SVG. 
 	* D3 zorgt ervoor dat we de functies van D3 kunnen aanroepen. 
 	* Select selecteert 1 element van de DOM in dit geval de body. 
 	* vervolgens wordt het SVG toegevoegd met (append) en 
 	* krijgt het nog hoogte en breedte mee (attr. van attribute).
 	
 	~~~~
	//Create SVG
		var svg = d3.select("body")
				.append("svg")
				.attr("width", w)
				.attr("height", h);
	~~~~


### Data
Data inladen, ‘binden’ is de volgende stap. Je kunt D3 koppelen met data van .csv of in dit geval .json files. 

1. plaats het bestand landen.json in je js folder
2. neem het volende script over

	~~~~
	//Load in GeoJSON data
		d3.json("/js/landen.json", function(json) {

	//Bind data and create one path per GeoJSON feature
			svg.selectAll("path")
				   .data(json.features)
				   .enter()
				   .append("path")
				   .attr("d", path);
			});	
	~~~~			
			
3. check in je browser of je een wereldkaart ziet
4. Kijk op https://github.com/mbostock/d3/wiki/Geo-Projections voor verschillende projecties
5. Je kunt nu met de projection en zoom spelen 
6. probeer bijvoorbeeld in te zoomen op Nederland

	~~~~
	var projection = d3.geo.mercator()
				.center([4, 52])
				.translate([ w/2, h/2 ]                                       	 			.scale(1000);
	~~~~


### extra data
Nu kun je bijvoorbeeld ook de vd data als circle inladen.

1. plaats de vd.geojson in je js folder
2. neem het script over 
	
	~~~~
	d3.json("js/vd.geojson", function(data){
                    
     //Create a circle for each city
		svg.selectAll("circle")
  				.data(data.features)
  				.enter()
 	  			.append("circle")
  				.attr("cx", function(d) {
    		//[0] returns the first coordinate (x) of the projected value
  					return projection(d.geometry.coordinates)[0];
  					})
  					.attr("cy", function(d) {
    		//[1] returns the second coordinate (y) of the projected value
  					return projection(d.geometry.coordinates)[1];
  					})
  				.attr("r", 5)
  				.style("fill", "blue")
  				.style("opacity", 0.75);
		});
	~~~~              
                   
3. verander de kleur speel met de styles 

### meer weten?
* boek: interactive data visualization Scott Murray. of online via http://alignedleft.com/tutorials/d3/about
* volg de starter tutorials op youtube van bijvoorbeeld D3.vienno