# Arcs, nodes, and promises

## D3 Advanced Tutorial

### Introduction

Mike Bostock started D3.js whilst working at the [New York Times](http://www.nytimes.com/). In short, D3.js takes in some data and makes a graphic out of it, be it a chart or a map, using [Scalable Vector Graphics](https://en.wikipedia.org/wiki/Scalable_Vector_Graphics). It's great for online newspapers:

* SVG is rendered in the browser. If many people try to access a news item, the graphics don't weigh much into the newspaper's IT infrastructure.
* SVG is scalable. Typically, people check the news when they are on the go. They use their mobile phones with all sorts of screen sizes. The graphics can easily scale up and down without losing their sharpness.
* It's data-driven: you can easily prepare your graphics with dummy data. So when the actual news happens or an embargo is lifted, D3.js re-creates the graphic as the data comes in.

Actually, everyone creating graphics can benefit from these advantages! So let's look at some clever optimisations.

### Step 1: let's get some geographic data

In the [Leaflet beginners tutorial](https://github.com/maptime-ams/Leaflet-D3-workshop/blob/gh-pages/tutorial-leaflet-starter/README.md) we used a static GeoJSON file to render the V&D department stores on the map. In the [Leaflet advanced tutorial](https://github.com/maptime-ams/Leaflet-D3-workshop/blob/gh-pages/tutorial-leaflet-advanced/README.md), we requested the neighbourhoods of Amsterdam from PDOK using a WFS service that returned GeoJSON. Particularly for polygons, the size of the GeoJSON grows quickly. Furthermore, the coordinates typically come with many decimal places, well beyond the screen resolution of your computer display! Boundaries don't change that often, so let's use this to our advantage.

Let's revisit how we requested the Amsterdam neighbourhoods from [PDOK](http://www.pdok.nl) using the WFS protocol:

````
https://geodata.nationaalgeoregister.nl/wijkenbuurten2014/ows?service=WFS&version=2.0.0&request=GetFeature&outputFormat=application/json&srsName=EPSG:4326&typeName=wijkenbuurten2014:cbs_buurten_2014&propertyName=buurtnaam,personenautos_per_huishouden,geom&cql_filter=gemeentenaam%20=%20%27Amsterdam%27
````

Let's request only the _permanent_ properties and store this in a file:
- the boundaries (geom)
- an identifier (buurtcode)
- neighbourhood name (buurtnaam)

Simply paste the following URL in your web browser and save it to disk as _neighbourhoods-pdok.geojson_:

````
https://geodata.nationaalgeoregister.nl/wijkenbuurten2014/ows?service=WFS&version=2.0.0&request=GetFeature&outputFormat=application/json&srsName=EPSG:4326&typeName=wijkenbuurten2014:cbs_buurten_2014&propertyName=buurtcode,buurtnaam,geom&cql_filter=gemeentenaam%20=%20%27Amsterdam%27
````

[Full demo 1](http://maptime-ams.github.io/Leaflet-D3-workshop/tutorial-d3-advanced/1/).

### Step 2: let's reduce the accuracy (and file size)

Did you notice the 15 decimals in the GeoJSON file you downloaded? That means the coordinates are [accurate to within 0.1 nanometer](http://uxblog.idvsolutions.com/2013/11/silly-geographic-precision.html)! All in all, the file is 533K! For most maps, 6 decimals is sufficient. Instead of stripping the superfluous decimals one by one by hand, we use [ogr2ogr](http://www.gdal.org/ogr2ogr.html) or [QGIS](http://www.qgis.org) to strip the decimals. Here's the example using ogr2ogr:

````
ogr2ogr -f GeoJSON neighbourhoods.geojson neighbourhoods-pdok.geojson -lco COORDINATE_PRECISION=6 -skipfailures
````

In QGIS, you open the GeoJSON file _neighbourhoods-pdok.geojson_ as a vector layer and save it again as GeoJSON. Make sure you specify the number of decimals in the save file menu. Now, the file size of the new file _neighbourhoods.geojson_ is 351K. Does the map load any quicker?

[Full demo 2](http://maptime-ams.github.io/Leaflet-D3-workshop/tutorial-d3-advanced/2/).

### About geometry and topology
