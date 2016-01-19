# Leaflet and PDOK

## Leaflet Advanced Tutorial

### Introduction

[Publieke Dienstverlening Op de Kaart](http://www.pdok.nl) (PDOK) (or _Public Service on the Map_) is a Dutch government initiative to provide a wide range of geographic web services. Many of these services provide data sets that are available under an **open** license. Typically, you would use a desktop GIS application or the [OpenLayers](http://www.openlayers.org) JavaScript library to use these services. This is the where the [Open Geospatial Consortium](http://www.opengeospatial.org) (OGC) standards for geographic web services were first implemented. Nevertheless, there are a number of ways you can just as well use Leaflet to consume these geographic web services for your online mapping application and leverage the potential of the wealth of data available from PDOK.

There is one caveat: maps from Google, Bing, OpenStreetMap come in the [Web Mercator](https://en.wikipedia.org/wiki/Web_Mercator) projection, whereas for the Netherlands the projection used is Rijksdriehoekstelsel (RD) or Amersfoort New. Each spatial reference system is identified by a code. The code for Web Mercator is [EPSG:3857](http://epsg.io/3857). The code for Amersfoort New is [EPSG:28992](http://epsg.io/28992). A third spatial reference system to keep in mind is WGS-84, the coordinates used by GPS systems that are recorded in longitude and latitude, identified by the code [EPSG:4326](http://epsg.io/4326).

For this tutorial, we will be using [Leaflet.js 1.0.0 beta 2](http://mourner.github.io/Leaflet/reference.html):

````html
<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet/v1.0.0-beta.2/leaflet.css" />
<script src="http://cdn.leafletjs.com/leaflet/v1.0.0-beta.2/leaflet.js"></script>
````

### Step 1: Simply add a map image from PDOK

The Open Geospatial Consortium (OGC) has created the [Web Mapping Service](https://en.wikipedia.org/wiki/Web_Map_Service) (WMS) protocol for the retrieval of geographically referenced images. Most data sets from PDOK are available as a WMS service. To get a sense of the range of services, you can browse the [Nationaal Georegister](http://www.nationaalgeoregister.nl/). For this tutorial we'll add a map of the number of cars per household for each neighbourhood.

````javascript
var cbs_cars = L.tileLayer.wms('http://geodata.nationaalgeoregister.nl/wijkenbuurten2014/wms', {
    layers: 'cbs_buurten_2014',
    styles: 'wijkenbuurten_thema_buurten_gemeentewijkbuurt_gemiddeld_aantal_autos_per_huishouden',
    format: 'image/png',
    attribution: '<a href="http://www.cbs.nl/">Centraal Bureau voor de Statistiek</a>',
}).addTo(map);
````

* The service endpoint for neighbourhood statistics from 2014 is `http://geodata.nationaalgeoregister.nl/wijkenbuurten2014/wms`.
* The visualisation of cars per household is defined in the style `wijkenbuurten_thema_buurten_gemeentewijkbuurt_gemiddeld_aantal_autos_per_huishouden`.
* Leaflet requests the images with MIME-type `image/png`.
* The data is attributed to `Statistics Netherlands`.

Leaflet calculates the coordinates of the bounding box at each zoom level to supply with the request. Also, Leaflet sets the default width and height to be 256 pixels. Finally, Leaflet uses the default Web Mercator projection in its requests. See how all tiles appear as you load the map in your Web browser. For the full WMS requests, use your favorite debugging tools.

[Full demo 1](http://maptime-ams.github.io/Leaflet-D3-workshop/tutorial-leaflet-advanced/1/).

Try and add images for the elevation data to your map. You can search the Nationaal Georegister website using the term `AHN` to find the appropriate service endpoint.

### Step 2: One large map instead of many smaller ones

Using the WMS protocol, each map image is generated just for you there and then. The mapping engine finds the data for the area you are looking at and applies the cartographic styles before sending back the image. In case many people are using your online mapping application, you can reduce the load on the mapping service by requesting one large image instead of many smaller ones.

Leaflet focuses on serving small map tiles, but fortunately there is the [Leaflet WMS](https://github.com/heigeo/leaflet.wms) plugin to retrieve large map images that follow the same WMS protocol. Pull in the plugin from Github through Rawgit to have it served with the proper HTTP headers so Web browsers understand that it is JavaScript:

````html
<script src="https://cdn.rawgit.com/heigeo/leaflet.wms/gh-pages/leaflet.wms.js"></script>
````

For this step, we'll add a map of the number of cars per household for each neighbourhood again, but use one large image instead:

````javascript
var cbs_cars = L.WMS.overlay('http://geodata.nationaalgeoregister.nl/wijkenbuurten2014/wms', {
    'layers': 'cbs_buurten_2014',
    'styles': 'wijkenbuurten_thema_buurten_gemeentewijkbuurt_gemiddeld_aantal_autos_per_huishouden',
    'format': 'image/png',
}).addTo(map);
````

Check which application appears to be more responsive: multiple small map images or one large map image?

[Full demo 2](http://maptime-ams.github.io/Leaflet-D3-workshop/tutorial-leaflet-advanced/2/).

### Step 3: Change the map style

Until now, we have requested map images from PDOK. Another type of geographic web services allows you to request geographic objects using the [WFS](https://en.wikipedia.org/wiki/Web_Feature_Service) protocol. The default response from a WFS service formulates the data as GML, an XML format. Fortunately, there is also the option to request the objects as GeoJSON, another data format that Leaflet does understand. Thus, you can apply a map style to the geographic objects in your online mapping application.

First, add a GeoJSON layer to your map and define custom functions to add cartographic styles and map interaction:

````javascript
var geojson = L.geoJson(null,
    {
        style: getStyle
        onEachFeature: onEachFeature
    }
).addTo(map);
````

Then, you set up the WFS endpoint for the neighbourhood map of 2014 from CBS:

````javascript
var url = 'https://geodata.nationaalgeoregister.nl/wijkenbuurten2014/ows?';
````

Leaflet expects objects as GeoJSON. Its coordinates have to be in WGS-84 (EPSG:4326): These parameters are identical for all GeoJSON-requests:

var params = 'service=WFS&version=2.0.0&request=GetFeature&outputFormat=application/json&srsName=EPSG:4326&';

Then we set the name of the layer that contains the neighbourhoods and specify the attributes we would like to use for our map and we filter out just the neighbourhoods of Amsterdam:

````javascript
params += 'typeName=wijkenbuurten2014:cbs_buurten_2014&';
params += 'propertyName=gemeentecode,personenautos_per_huishouden,geom&';
params += 'cql_filter=gemeentenaam \= \'Amsterdam\'';
````

We have defined a custom function to specify the cartographic style. We set up some basic look and feel first and vary the colour of each neighbourhood depending on the number of cars per household:

````javascript
function getStyle(features) {
    var rate = features.properties.personenautos_per_huishouden;
    return {
            color: '#fff',
            fillColor: getColour(rate),
            weight: 0.8,
            opacity: 1,
            fillOpacity: 0.9
        }
}
````

Need some colour inspiration? Check out [ColorBrewer](http://colorbrewer2.org/). Fortunately, the various colour ranges have been incorporated into [Chroma.js](), a tiny JavaScript library for dealing with colours!

````html
<script src="https://cdnjs.cloudflare.com/ajax/libs/chroma-js/1.1.1/chroma.min.js"></script>
````

Let's set the colours using the [YlOrBr](http://colorbrewer2.org/?type=sequential&scheme=YlOrBr&n=8) colour range:

````javascript
function getColour(d) {
    var colour,
    colorScale = chroma
        .scale('YlOrBr')
        .domain([0,1.5]);
    if (d < 0) {
        colour = '#eee'
    } else {
        colour = colorScale(d).hex()
    }
    return colour;
}
````

See the source code of the demo to find out how the cartographic style changes based on mouse interaction!

[Full demo 3](http://maptime-ams.github.io/Leaflet-D3-workshop/tutorial-leaflet-advanced/3/).
