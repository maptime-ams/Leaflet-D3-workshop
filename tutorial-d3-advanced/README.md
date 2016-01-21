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

### Step 3: about geometry and topology

Neighhourhoods share a lot of boundaries. So why store every boundary twice, i.e. for every neighbourhood? Furthermore, coordinates in longitude and latitude are floating point values. It's much easier for computers to deal with integers instead. This is what [TopoJSON](https://github.com/mbostock/topojson) is all about: coordinates are transformed to integers and only the topological structure (its nodes and arcs) are stored. Also, it keeps track which arcs together make up a polygon. Finally, you can add a little line generalisation.

TopoJSON is not something that comes out of the box when you are working with online mapping services like the ones PDOK provides. So we have to make our own. It's well worth the effort: geometries don't change as often as all accompanying the neighbourhood statistics and the size reduction is noticeable!

You can either [install TopoJSON](https://github.com/mbostock/topojson/wiki/Installation) and run it as a command line tool, or you can use online translators to create your own TopoJSON files. A favourite of ours is [MapShaper](http://www.mapshaper.org/) as you can interactively adjust the line generalisation before outputting to TopoJSON. Using the command line tool, we were able to reduce the filesize from 351K down to only 22K!

````
topojson --id-property buurtcode -p name=buurtnaam -q 1e5 -s 0.000000001 -o neighbourhoods.topojson neighbourhoods.geojson
````

Fair enough, we did indeed cut some corners here and there in the process. So, don't settle any border disputes based on the TopoJSON file, but for online mapping it does the job! Now we need to extend our mapping application to be able to consume TopoJSON:

````html
<script src="http://d3js.org/topojson.v1.min.js"></script>
````

[Full demo 3](http://maptime-ams.github.io/Leaflet-D3-workshop/tutorial-d3-advanced/3/).

### Step 4: add some colour, right on cue (uh, queue actually)!

In step 2, we dropped the neighbourhood statistics from our GeoJSON file. Since we left in the neighbourhood code, we can still match the GeoJSON/TopoJSON with other data sources. However, we now simply use the same WFS protocol to only get some statistics for the Amsterdam neighbourhoods. We add in _personenautos_per_huishouden_ again and change value for the parameter `outputFormat` from _application/json_ to _csv_:

````
https://geodata.nationaalgeoregister.nl/wijkenbuurten2014/ows?service=WFS&version=2.0.0&request=GetFeature&outputFormat=csv&srsName=EPSG:4326&typeName=wijkenbuurten2014:cbs_buurten_2014&propertyName=buurtcode,personenautos_per_huishouden&cql_filter=gemeentenaam%20=%20%27Amsterdam%27
````

Put the URL in a web browser and save the output in a text file _carownership.csv_. We need to orchestrate loading two files at a time:
- neighbourhoods.topojson
- carownership.csv

Only when both files have been loaded by the browser, the rest of our code can be executed. Yes, asynchronous JavaScript is awesome, but orchestrating this kind of thing is a hassle. Well, not really now there is [queue.js](https://github.com/mbostock/queue), another asynchronous helper library for JavaScript:

````html
<script src="http://d3js.org/queue.v1.min.js"></script>
````

Then load both files into your mapping application and match up the neighbourhoud statistics using the neighbourhood code:

````javascript
queue()
    .defer(d3.json, "neighbourhoods.topojson")
    .defer(d3.csv, "carownership.csv", function(d) { rateById.set(d.buurtcode, +d.personenautos_per_huishouden);})
    .await(ready);
````

Check out the demo below to see it in action. Any chance you are able to pull in a [ColorBrewer](http://colorbrewer2.org/) colour palette?

[Full demo 4](http://maptime-ams.github.io/Leaflet-D3-workshop/tutorial-d3-advanced/4/).
