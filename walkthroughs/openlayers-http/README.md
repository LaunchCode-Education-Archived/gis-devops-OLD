---
title: "Walkthrough: OpenLayers and jQuery"
---

Follow along with the instructor as we get started with jQuery and OpenLayers.

## Getting Started

Fork and clone the [airwaze-studio](https://gitlab.com/LaunchCodeTraining/airwaze-studio) project. Import it into IntelliJ.

Create a new html file called `index.html` under `src/main/resources/static`. Start with the following code:

```html
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

Next, add OpenLayers to your project by including the JavaScript source in the head.
```html
 <script src="https://openlayers.org/en/v4.6.4/build/ol.js"
         type="text/javascript"></script>
 ```

Next add a link for JQuery. You can find the current version at [code.jquery.com](https://code.jquery.com). The minified version is fine. We'll use jQuery to make things like AJAX calls and DOM manipulation a bit easier.

Put the placeholder for the map on the page, within the `<body>`. This `div` will be replaced with the contents of the rendered map.
 ```html
<div id="mapPlaceholder" class="map"></div>
 ```

 Then set the size for the map.
 ```html
<style>
.map {
    height: 400px;
    width: 100%;
}
</style>
 ```

Let's add a map to our page. To do so, place the following script in the `<head>`, below the OpenLayers and jQuery includes.
```js
$(document).ready(function(){
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
});
```

This creates a map that can contain multiple layers in the `layers: []` object. In this case we are using a Tile layer. You can find all of the layer types [here](https://openlayers.org/en/v4.6.4/apidoc/ol.source.html).

The `view` attribute allows us to position the map at a certain lat long and zoom level using an `ol.View` object.

## Add GeoJSON data

Let's pull in some GeoJSON data. Open the [openflights-geojson](https://github.com/node-geojson/openflights-geojson) repo.

Per the instructions, run the following command to install package:

```nohighlight
$ npm install -g openflights-geojson
```

Then create a JSON file for use in the project by running the following command in `src/main/resources/static/json/`:
```nohighlight
$ openflights-airports > airports.geojson
```

<aside class="aside-warning" markdown="1">
Opening `airports.geojson` with IntelliJ will cause your computer some hardship.

Instead, view the file in Terminal with `less airports.geojson`. There will be about 14,000 reports in that file.
</aside>

To view our file, let's fire up a simple local webserver using Python. Run this command from `src/main/resources/static/`:
```nohighlight
$ python -m http.server 8080
```

Then navigate to `localhost:8080` in your browser. You'll see the page render, but no map present yet. That's because we haven't added any data to the map. We'll do that now.

Let's make an AJAX call to retrieve the JSON using the Chrome console. Open up the Chrome inspector and paste the following into the console: 
```js
$.getJSON('http://localhost:8080/json/airports.geojson', {}).done(function(json) { console.log(json)});
```

You should receive the JSON back in the console. If your callback for the AJAX call is not firing, be sure to check that the contents of `airports.json` are valid JSON using a [JSON Validator](https://jsonlint.com/).

Once you see JSON dumped to the console, copy/paste that code snippet into your `index.html` file, just below the mapping code added previously, inside of the `ready` handler. Refesh the page in your browser, and ensure you still see the same JSON dumped to the console.

Now, Instead of printing all of the JSON, let's print each report individually.

Add a `<ul id="airportList">` to the HTML file, along with the following code to the page to create a list of all the reports.

```js
for (var i=0; i<json.features.length; i++) {
    $('#airportList').append('<li>' + json.features[i].type + '</li>');
});
```

Next, let's add these reports to the map.

## Add a click handler

Let's add a click handler to the map. First, let's try it out in the console. Paste the following code in the console:

```js
map.on('click', function(event) {
    map.forEachFeatureAtPixel(event.pixel, function(feature, layer) {
        console.log(feature);
    });
});
```

Now when you click on the map, it will print that feature to the console. Inspect the object to make sure it is what you expected. Next take a look at the [documentation for ol.Feature](https://openlayers.org/en/latest/apidoc/ol.Feature.html). How can you access the data on that object?

Use the `get()` method on `ol.Feature`:
```js
map.on('click', function(event) {       
    map.forEachFeatureAtPixel(event.pixel, function(feature,layer) {
        console.log(feature.get('dataField'));
    });
});
```

We want to add data about this report to our map dashboard. In plain HTML, code out what it could like on the page. Use this as a guide:

```html
<div id="airportList">
    <h3>Aalen-Heidenheim/Elchingen Airport</h3>
    <p>ICAO: EPDA</p>
    <p>Location: Aalen-heidenheim, Germany</p>
    <p>Altitude: 1916</p>
    <p>Time Zone: Europe/Berlin</p>
</div>
```

Once you feel good about how it looks, use [Javascript Template Literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) to populate the feature data into the HTML. Here is what your code should look like:
```js
map.on('click', function(event) {
    map.forEachFeatureAtPixel(event.pixel, function(feature,layer) {
        $('#airportList').empty();
        $('#airportList').append(`<div id="airportList"><h3>${feature.get('locationType')}</h3><p>ICAO: ${feature.get('icao')}</p><p>Location: ${feature.get('dataField')}, ${feature.get('country')}</p><p>Altitude: ${feature.get('alt')}</p><p>Time Zone: ${feature.get('tz')}</p></div>`);
    });
});
```

## Troubleshooting

Invalid JSON will cause the jQuery callback to not fire. If you are having an issue, validate the JSON that you are sending across the wire. Here is a [JSON Validator](https://jsonlint.com/).

Invalid GeoJSON will not show up on your map. You will see the data pull back, but you will not see any data displayed. Validate your GeoJSON. Here is a [GeoJSON Validator](http://geojson.io/o).

If you get the message `The element type "link" must be terminated by the matching end-tag "</link>".`, then add the following tag:  
 `<html lang="en" xmlns:th="http://www.thymeleaf.org/">`;

If you get the message `The content of elements must consist of well-formed character data or markup.`, then change your inline javascript tags to `<script>/*<![CDATA[*/ ..... /*]]>*/</script>`. Here is a [StackOverflow question](ihttps://stackoverflow.com/questions/4338538/error-parsing-xhtml-the-content-of-elements-must-consist-of-well-formed-charact/4338816) that discusses this.
