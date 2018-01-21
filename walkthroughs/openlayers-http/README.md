---
title: "Walkthrough: OpenLayers and jQuery"
---

Follow along with the instructor as we get started with jQuery and OpenLayers.

## Getting Started

First, we need to disable CORS for our server.  We'll explain later, but for now, this makes things simpler.
* Add the following plugin to your browser [CORS Toggle](https://chrome.google.com/webstore/detail/cors-toggle/jioikioepegflmdnbocfhgmpmopmjkim/related?hl=en).
* Click the "CORS" button in your browser bar to turn off CORS (it should turn green).

Create a new html file called "index" under src/main/resources/static.  Start with the following code:

```
<!doctype html>
<html lang="en">
<head>
  <title>OpenLayers example</title>
</head>
<body>
<h2>My Map</h2>

</body>
</html>
```

Next add OpenLayers to your project.
```
 <script src="https://openlayers.org/en/v4.6.4/build/ol.js" type="text/javascript"></script>
 ```

Next add a link for JQuery.  You can find the current version at [code.jquery.com](https://code.jquery.com). The minified version is fine.

Put the placeholder for the map on the page.
 ```
   <div id="mapPlaceholder" class="map"></div>
 ```

 Then set the size for the map.
 ```
   <style>
     .map {
       height: 400px;
       width: 100%;
     }
   </style>
 ```

The following script will add a map to your project
```
    var map = new ol.Map({
        target: 'mapPlaceholder',
        layers: [
            new ol.layer.Tile({
                source: new ol.source.OSM()
            })
        ],
        view: new ol.View({
            center: ol.proj.fromLonLat([37.66,30.88]),
            zoom: 4
        })
    });
```

***Note:  Be sure to mention Bing maps and you can easily change the map type***

This creates a map that can contain multiple layers in the ```layers: []``` object.

In this case we are using a Tile layer.  You can find all of the layer types [here](https://openlayers.org/en/v4.6.4/apidoc/ol.source.html).

The ```new ol.View``` allows you to position the map at a certain lat long and zoom level.

# Add GeoJSON data

Let's pull in some geojson data.  Open the [openflights-geojson](https://github.com/node-geojson/openflights-geojson) repo.

Per the instructions, run ` npm install -g openflights-geojson` to install the utility.  Then run `openflights-airports > airports.geojson` to create the file.

*Warning: Opening `airports.geojson` with IntelliJ will cause your computer some hardship

Instead view the file with `less airports.geojson`.  There should be about 14,000 reports in that file.  

Create the directory `src/main/resources/static/json` and place `airports.geojson` in the directory.

Take a look at the file using a basic web server. It should live at `localhost:8080/` 

Let's make an AJAX call to retrieve the JSON; however, let's test the call in the Chrome console.  Open up the Chrome inspector and paste the following into the console: `$.getJSON('http://localhost:8080/json/airports.geojson', {}).done(function(json) { console.log(json)});`.

You should receive the JSON back in the console.  If your callback for the AJAX call is not firing, be sure to check that you are returning valid JSON.  Here is a [JSON Validator](https://jsonlint.com/).

Now let's plug that code into our project. Instead of printing all of the JSON, let's print each report individually.  Add the following script to your `index` file: `$.getJSON('http://localhost:8080/json/airports.geojson', {}).done(function(json) { console.log(json)});`

Hmm... not quite what we were looking for.  Open up the Chrome Debugger and let's find out how to access this object. 

Add the following code to the page to create a list of all the reports.
```
$('#airportList').append('<ul>' + json[i].properties.locationType + '</ul>');
```

Next, let's add these reports to the map.  


# Add a click handler
Let's add a click handler to the map.  First, let's try it out in the console.  Paste the following code in the console:
```
map.on('click', function(event) {

    map.forEachFeatureAtPixel(event.pixel, function(feature,layer) {
        console.log(feature);
    } ) 
} )
```

Now when you click on the map, it will print that feature to the console.  Inspect the object to make sure it is what you expected.  Next take a look at the [documentation for ol.Feature](https://openlayers.org/en/latest/apidoc/ol.Feature.html).  How can you access the data on that object?

Use the `get()` method on `ol.Feature`.  
```
map.on('click', function(event) {       

    map.forEachFeatureAtPixel(event.pixel, function(feature,layer) {
        console.log(feature.get('dataField'));
    } ) 
} )
```

We want to add data about this report to our map dashboard.  In plain HTML, code out what it could like on the page.  Use this as a guide:
```
<div id="airportList">
    <h3>Aalen-Heidenheim/Elchingen Airport</h3>
    <p>ICAO: EPDA</p>
    <p>Location: Aalen-heidenheim, Germany</p>
    <p>Altitude: 1916</p>
    <p>Time Zone: Europe/Berlin</p>
</div>
```

Once you feel good about how it looks, use [Javascript Template Literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) to populate the feature data into the HTML.  Here is what your code should look like:
```
map.on('click', function(event) {

    map.forEachFeatureAtPixel(event.pixel, function(feature,layer) {
        $('#airportList').empty();
        $('#airportList').append(`<div id="airportList"><h3>${feature.get('locationType')}</h3><p>ICAO: ${feature.get('icao')}</p><p>Location: ${feature.get('dataField')}, ${feature.get('country')}</p><p>Altitude: ${feature.get('alt')}</p><p>Time Zone: ${feature.get('tz')}</p></div>`);
    })
})
```

# Studio

Use the [openflights-geojson Github repository](https://github.com/node-geojson/openflights-geojson) to pull additional data about flights.


[Read about constructing GeoJSON](https://macwright.org/2015/03/23/geojson-second-bite)  
[OpenLayers Examples](https://openlayers.org/en/latest/examples/)  
[Adding and Removing Layers](http://www.acuriousanimal.com/thebookofopenlayers3/chapter02_01_adding_removing_layers.html)  
[Validate your GeoJSON!](http://geojson.io)  
[OpenLayers Drawing Examples](http://openlayers.org/en/latest/examples/geojson.html)


# Problems

Invalid JSON will cause the jQuery callback to not fire.  If you are having an issue, validate the JSON that you are sending across the wire.  Here is a [JSON Validator](https://jsonlint.com/).

Invalid GeoJSON will not show up on your map.  You will see the data pull back, but you will not see any data displayed.  Validate your GeoJSON.  Here is a [GeoJSON Validator](http://geojson.io/o).

If you get the message `The element type "link" must be terminated by the matching end-tag "</link>".`, then add the following tag:  
 `<html lang="en" xmlns:th="http://www.thymeleaf.org/">`

If you get the message `The content of elements must consist of well-formed character data or markup.`, then change your inline javascript tags to `<script>/*<![CDATA[*/ ..... /*]]>*/</script>`.  Here is a [StackOverflow question](ihttps://stackoverflow.com/questions/4338538/error-parsing-xhtml-the-content-of-elements-must-consist-of-well-formed-charact/4338816)
