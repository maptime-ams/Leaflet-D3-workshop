# Leaflet tutorial
## mijn eigen webmap

### Leaflet 
Maak interactieve webmaps voor al je devices. Vladimir Agafonkin maakte het en is een opensource javascript library. Dus gratis en vrij te gebruiken. Weet je al wat van HTML, CSS en JavaScript dan kun je snel aan de slag. Als dit niet gesneden koek voor je is dan kun je met deze tutorial ook al heel goed een start maken.

### wat heb je nodig
* beetje kennis van van HTML, CSS, en JavaScript
* teksteditor voor je HTML code, bijv. brackets, sublime text, …
* internettoegang
* browser, chrome heeft een fijne debugger

### de eerste kaart
1. open je teksteditor 
2. start met een basis HTML pagina
3. sla op in een nieuwe folder en noem deze index.html


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


Open http://leafletjs.com/download.html
scroll naar beneden en knip plak de meeste recente leaflet bibliotheek.

<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />
<script src=“http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script>


Zet de <link….leaflet.css”/> in de <head>
zet de De leaflet bibliotheek <script…..leaflet.js”/> in de body.  Vaak zie je dit ook in de <head> staan. Het is sneller als je het juist zo ver mogelijk onderaan doet. 
Open een nieuw bestand. Noem bestand bijvoorbeeld ‘ main.css’  en plaats het in een nieuwe map: ‘style’ in dezelfde folder als je index.html. 
Je kunt evt. ook gelijk mappen aanmaken voor:  ‘images’  en ‘ js’. 
Open je ‘index.html’ bestand en zet de link naar je css bestand in je <head> : 

<link rel="stylesheet" href=“style/main.css”/>

verander de titel in bijv. “mijn eerste kaart met leaflet”

Zet een ‘div’  in de <body>:  

	<div id=“map”></div>

de basis is klaar! Geef je “map” altijd een hoogte (en evt breedte). Dit doe je in de main.css

	#map { height: 300px; width:100%;} 
 
speel met de aantal px en/of % tot je een mapformaat hebt wat je goed vindt.


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

Voor de echte kaart heb je een baselayer nodig. Dit is je ondergrond kaart. Deze is opgebouwd uit tegels, dat laadt lekker snel. 
Neem het <script>…..</script> over


<!doctype html>

<html>
 	<head>
    		<title>basis HTML</title>  
		 <link rel="stylesheet" href="style/main.css"/>
		<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />
   		
	 
 	</head>
       
 	<body>
     		<H1>voorbeeld</H1>
		<div id=“map”></div>
<script>
//initialize the map         
var map = L.map('map').setView([52.18, 5.5308], 11);
         
//maak een baselayer - tegels         
var achtergrondkaart = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png', {
    attribution: '<a href="http://openstreetmap.org">OpenStreetMap</a>contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>',
    maxZoom: 19
});
          
achtergrondkaart.addTo(map);
</script>
           <script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script> 

 	</body>
</html>


Je hebt nu een kaart gemaakt.
	var map =  L.map(“map” ): is het initialiseren van een “map” variabele
	setView() is een manier voor het centreren van de map (latitude, longitude, zoom level). de 	projectie is in googlemercator. 
	Vervolgens voegden we een baselayer van tegels toe. Bijvoorbeeld van OpenStreetMap. 
	addTo() toevoegen van de laag aan de map

Wil je een specifieke plaats in het centrum. Zoek de coordinaten hier: 
http://www.mapcoordinates.net/en		


Een andere gratis tegelboer is maps.stamen.com. Deze brengen zelfs drie varianten	
	•	http://tile.stamen.com/toner/{z}/{x}/{y}.png
	•	http://tile.stamen.com/terrain/{z}/{x}/{y}.jpg
	•	http://tile.stamen.com/watercolor/{z}/{x}/{y}.jpg


Oefen met verschillende tegels, coordinaten en zoomlevels en maak zo je eigen basiskaart.


voeg handmatig markers, cirkels en polygonen toe
We gaan een custom marker toevoegen(je eigen woonplaats bijv.). Kies in ieder geval coordinaten die op je kaart liggen.

	var marker = L.marker([51.5, -0.09]).addTo(map);

Voeg nog 4 markers toe. (vrienden, of plaatsen waar je ooit gewoond hebt?). Let er wel op dat je steeds de ‘var’ een andere naam geeft. bijv:

	var monique = L.marker([52.070, 4.300]).addTo(map);   

Geef elke marker een popup. bij var monique maken we deze popup

	var popup = “schrijf hier je tekst tussen aanhalingstekens.";
	monique.bindPopup(popup); 


Zo plaats je een cirkel op de kaart, gebruik je eigen coordinaten	

	var circle = L.circle([51.508, -0.11], 500, {
    		color: 'red',
    		fillColor: '#f03',
    		fillOpacity: 0.5
		}).addTo(map);


 Verbind de markers met een polygoon. Gebruik hiervoor de al eerder gebruikte coordinaten in de juiste volgorde.


	var polygon = L.polygon([
 	   [51.509, -0.08],
	    [51.503, -0.06],
	    [51.51, -0.047]
	]).addTo(map);


<!doctype html>

<html>
 	<head>
    		<title>basis HTML</title>  
		 <link rel="stylesheet" href="style/main.css"/>
		 <link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />
   		
	 
 	</head>
       
 	<body>
     		<H1>voorbeeld</H1>
		<div id=“map”></div>

 <script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script> 

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
    
    ]).addTo(ma
              
</script>
        
 	</body>
</html>


check ook de debugger of alles klopt… (f12, alt-cmd-i)








Voeg een nieuwe laag toe.
geoJson
geoJson is de standaard qua data type om webmaps mee te maken. Met leaflet kun je deze gemakkelijk koppelen. 

Meer weten over geoJson lees je hier https://en.wikipedia.org/wiki/GeoJSON

plaats de jQuery script onder je leaflet script in de body.

	<script src="http://code.jquery.com/jquery-2.1.0.min.js"></script>  

neem het script over, je opent en plaats de data van de geoJson op de kaart.

 	$.getJSON("js/vd.geojson",function(data){
    		var vdIcon = L.icon({
       		iconUrl: "images/vd.png",
        		iconSize: [20,20]
    	});
    	L.geoJson(data,{
        		pointToLayer: function(feature,latlng){
            	return L.marker(latlng, {icon: vdIcon});
        		}
    	}).addTo(map);   
});


<!doctype html>

<html>
 	<head>
    		<title>basis HTML</title>  
		 <link rel="stylesheet" href="style/main.css"/>
		<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.css" />
   		<script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script>  
		

	 
 	</head>
       
 	<body>
     		<H1>voorbeeld</H1>
		<div id=“map”></div>

<script src="http://cdn.leafletjs.com/leaflet/v0.7.7/leaflet.js"></script> 
<script src="http://code.jquery.com/jquery-2.1.0.min.js"></script>    

<script>
//initialize the map         
var map = L.map('map').setView([52.18, 5.5308], 11);
         
//maak een baselayer - tegels         
var achtergrondkaart = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png', {
    attribution: '<a href="http://openstreetmap.org">OpenStreetMap</a>contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>',
    maxZoom: 19
});
          
achtergrondkaart.addTo(map);

        
//voeg evt meerder tilelayers toe opdezelfde manier.
//geojson toevoegen van extern bestand
$.getJSON("js/vd.geojson",function(data){
    var vdIcon = L.icon({
        iconUrl: "images/vd.png",
        iconSize: [20,20]
    });
    L.geoJson(data,{
        pointToLayer: function(feature,latlng){
            return L.marker(latlng, {icon: vdIcon});
        }
    }).addTo(map);   
});
</script>
           
		</body>
</html>


meer styling tips
Duizelt het al? leer eerst de basics van HTML bij codecademy: https://www.codecademy.com/learn/web
codeacedemy is ook heel handig voor je skills van oa javascript te verbeteren.
wil je een speciaal font gebruiken? die kun je bijvoorbeeld bij ‘ google fonts’ vinden zie muehlenhaus webcartography 3 op youtube


andere plekken om te oefenen
http://maptimeboston.github.io/leaflet-intro/
http://leafletjs.com/examples/quick-start.html
http://maptimeboston.github.io/leaflet-intro/
online boekleaflet tips and tricks, interactive maps made easy by Malcolm Maclean. hier te lezen https://leanpub.com/leaflet-tips-and-tricks
bekijk ook de filmpjes over webcartography geoJson and Leaflet van Ian Muehlenhaus https://www.youtube.com/playlist?list=PLOlUoOtyTOXilBfrGanBziU738fyJdFqn
