# leaflet tutorial
## mijn eigen webmap

### leaflet 
Maak interactieve webmaps voor al je devices. Vladimir Agafonkin maakte het en is een opensource javascript library. Dus gratis en vrij te gebruiken. Weet je al wat van HTML, CSS en JavaScript dan kun je snel aan de slag. Als dit niet gesneden koek voor je is dan kun je met deze tutorial ook al heel goed een start maken.

### wat heb je nodig
* beetje kennis van van HTML, CSS, en JavaScript
* teksteditor voor je HTML code, bijv. brackets, sublime text
* internettoegang
* browser, chrome heeft een fijne debugger

### de eerste kaart
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
	

4. Open http://leafletjs.com/download.html
5. scroll naar beneden en knip de meeste recente leaflet bibliotheek

	~~~~
	<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css"/>
	
	<script src=“http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script>
	~~~~
	
		
6. plak de leaflet.css in de head
7. plak de leafletjs bibliotheek in de body. 
	* Vaak zie je dit ook in de head staan. Het is sneller als je het juist zo ver mogelijk onderaan doet. 

8. Open een nieuw bestand. Noem bestand bijvoorbeeld ‘ main.css’  en plaats het in een nieuwe map: ‘style’ in dezelfde folder als je index.html. 
9. Je kunt evt. ook gelijk mappen aanmaken voor:  ‘images’  en ‘ js’. 
10. Open je ‘index.html’ bestand en zet de link naar je css bestand in je head

	~~~~
	<link rel="stylesheet" href=“style/main.css”/>
	~~~~

11. verander de titel in bijv. “mijn eerste kaart met leaflet”
12. Zet een ‘div’ in de body 

	~~~~
	<div id=“map”></div>
	~~~~

13. de basis is klaar! Geef je “map” altijd een hoogte (en evt breedte). Dit doe je in de main.css

	~~~~
	#map { height: 300px; width:100%;} 
	~~~~
 
14. speel met de aantal px en/of % tot je een mapformaat hebt wat je goed vindt.

	~~~~
	<!doctype html>
	<html lang="nl">
	<html>
	 	<head>
			<meta charset="utf-8">
	    	<title>Mijn eerste kaart met Leaflet</title>  
			<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />
			<link rel="stylesheet" href="style/main.css"/>
		</head>   
	 	<body>
	     		<H1>voorbeeld</H1>
				<div id=“map”></div>
				<script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script> 
	 	</body>
	</html>
	~~~~

### Baselayer	

Voor de echte kaart heb je een baselayer nodig. Dit is je ondergrond kaart. Deze is opgebouwd uit tegels, dat laadt lekker snel. 

1. Neem het script over en plaats in de body

	~~~~
		<script>
			//initialize the map         
			var map = L.map('map').setView([52.18, 5.5308], 11);
	        
			//maak een baselayer - tegels         
			var achtergrondkaart = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
			 	{
	    		attribution: '<a href="http://openstreetmap.org">OpenStreetMap</a>contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>',
	    		maxZoom: 19
				}
			);
	          
			achtergrondkaart.addTo(map);
		</script>       
	~~~~

2. Je hebt nu een kaart gemaakt.
	* var map =  L.map(“map” ): is het initialiseren van een “map” variabele
	* setView() is een manier voor het centreren van de map (latitude, longitude, zoom level). de 	projectie is in googlemercator. 
	* Vervolgens voegden we een baselayer van tegels toe. Bijvoorbeeld van OpenStreetMap. 
	* addTo() toevoegen van de laag aan de map
	*

3. Wil je een specifieke plaats in het centrum. Zoek de coordinaten hier: 
http://www.mapcoordinates.net/en		


4. Een andere gratis tegelboer is maps.stamen.com. Deze brengen zelfs drie varianten

	* http://tile.stamen.com/toner/{z}/{x}/{y}.png
	* http://tile.stamen.com/terrain/{z}/{x}/{y}.jpg
	* http://tile.stamen.com/watercolor/{z}/{x}/{y}.jpg


5. Oefen met verschillende tegels, coordinaten en zoomlevels en maak zo je eigen basiskaart.


### markers, cirkels en polygonen
voeg handmatig markers, cirkels en polygonen toe (je eigen woonplaats bijv.). Kies in ieder geval coordinaten die op je kaart liggen.

1. Zet het volgende binnen het script

	~~~~
	var marker = L.marker([51.5, -0.09]).addTo(map);
	~~~~

2. Voeg nog 4 markers toe. (vrienden, of plaatsen waar je ooit gewoond hebt?). Let er wel op dat je steeds de ‘var’ een andere naam geeft. bijv:

	~~~~
	var monique = L.marker([52.070, 4.300]).addTo(map);   
	~~~~

3. Geef elke marker een popup. bij var monique maken we deze popup

	~~~~
	var popup = “schrijf hier je tekst tussen aanhalingstekens.";
	monique.bindPopup(popup); 
	~~~~


4. Zo plaats je een cirkel op de kaart, gebruik je eigen coordinaten

	~~~~
		var circle = L.circle([51.508, -0.11], 500, {
    		color: 'red',
    		fillColor: '#f03',
    		fillOpacity: 0.5
		}).addTo(map);
	~~~~

5. Verbind de markers met een polygoon. Gebruik hiervoor de al eerder gebruikte coordinaten in de juiste volgorde.

	~~~~
	var polygon = L.polygon([
 	   [51.509, -0.08],
	   [51.503, -0.06],
	   [51.51, -0.047]
	]).addTo(map);
	~~~~


6. je script ziet er als volgt uit. IkCheck ook de debugger of alles klopt… (f12, alt-cmd-i)
	
	~~~~
	<script>
	//initialize the map         
	var map = L.map('map').setView([52.18, 5.5308], 11);
	         
	//maak een baselayer - tegels         
	var achtergrondkaart = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png', {
	    attribution: '<a href="http://openstreetmap.org">OpenStreetMap</a>contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>',
	    maxZoom: 19
	});
	          
	achtergrondkaart.addTo(map);
	       
	//voeg een willekeurige marker toe                       
	var monique = L.marker([52.070, 4.300]);         
	monique.addTo(map);             
	    
	var miranda = L.marker([53.201, 5.799]);         
	miranda.addTo(map);            
	
	var barbel = L.marker([52.351, 4.620]);         
	barbel.addTo(map);  
	         
	var bea = L.marker([51.560, 5.091]);         
	bea.addTo(map);  
	
	//popup toevoegen
	var popup = "Monique woont in Den Haag dat is 1 uur en 15 min rijden.";
	monique.bindPopup(popup);   
	         
	var popup1 = "Barbel woon in Heemstede, dat is 1 uur rijden.";
	barbel.bindPopup(popup1)
	
	var popup2 = "Miranda woont in Leeuwarden, dat is 1 uur en 45 minuten rijden.";
	miranda.bindPopup(popup2);   
	
	var popup3 = "Bea woont in Tilburg, dat is 1 uur en 10 minuten rijden.";
	bea.bindPopup(popup3);   
	
	 //voeg een willekeurig circle toe         
	var circle = L.circle([52.156, 5.387], 4500, {
	    color: 'red',
	    fillColor: '#f03',
	    fillOpacity: 0.5
	}).addTo(map);  
	
	//voeg een willekeurige polygoon toe     
	var polygon = L.polygon([
	    [53.201, 5.799],
	    [52.351, 4.620],
	    [52.070, 4.300], 
	    [51.560, 5.091],
	    
	    ]).addTo(map);              
	</script>
	~~~~

### geoJson-tilelayer
geoJson is de standaard qua data type om webmaps mee te maken. Deze data kun je als kaartlaag toevoegen. 

* Meer weten over geoJson lees je hier https://en.wikipedia.org/wiki/GeoJSON

1. plaats het bestand vd.geojson in de js folder
2. plaats vd.png in je imagesfolder
3. neem het script over en check je kaart
    
	~~~~          
	//geojson zonder jQuery met xhr definieer eerst de icon
				
			var vdIcon = L.icon({
	            iconUrl: "images/vd.png",
	            iconSize: [20,20]
	        });
	//maak vervolgens een geojson laag
	        
	       var geojson = L.geoJson(null,{
	            pointToLayer: function(feature,latlng){
	                return L.marker(latlng, {icon: vdIcon});
	            }
	        }).addTo(map);           
	         
	//daarna stop je de daadwerkelijke geojson hierin
	
		var xhr = new XMLHttpRequest();
			xhr.open('GET', encodeURI("js/vd.geojson"));
			xhr.onload = function() {
	    	if (xhr.status === 200) {
	        geojson.addData(JSON.parse(xhr.responseText));
	    } else {
	        alert('Request failed.  Returned status of ' + xhr.status);
	    }
		};
		xhr.send();   
	~~~~
	           
### tips			
* leer de basics van HTML, javascript bij codecademy: https://www.codecademy.com/learn/web
* wil je een speciaal font gebruiken? check ‘google fonts’
* leuke opdracht vind je hier http://maptimeboston.github.io/leaflet-intro/
* lees het online boek 'leaflet tips and tricks, interactive maps made easy' by Malcolm Maclean. https://leanpub.com/leaflet-tips-and-tricks
* bekijk ook de filmpjes over webcartography geoJson and Leaflet van Ian Muehlenhaus. https://www.youtube.com/playlist?list=PLOlUoOtyTOXilBfrGanBziU738fyJdFqn
