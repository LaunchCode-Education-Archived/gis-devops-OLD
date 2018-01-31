---
title: "Studio: Airwazes"
---

Your goal is to add an additional layer that shows flight routes and display it on the map.  This time we will incorporate our map into a Spring Boot project. Some starter code has been provided for you.

##Set Up Project
Fork and clone this [Gitlab project](https://gitlab.com/LaunchCodeTraining/airwaze-studio). Then open the project in IntelliJ.

##Set Up PostGIS
To install PostGIS, run the following commands in terminal:
```nohighlight
$ brew update
$ brew install postgis
```
This is going to take a while.

Make sure your Postgresql server is running. Remember we used [https://postgresapp.com/](https://postgresapp.com/) to install Postgresql, which automatically created a service that we can start and stop via its UI in the menu bar.

Run `psql` CLI by double clicking on the `postgres` database in the Postgresql app.

Open the Postgres UI and double click on the `postgres` db to open a `psql` command prompt.
Then execute:
```nohighlight
# CREATE DATABASE airwaze;
```

Also create a database for your tests:
```nohighlight
# CREATE DATABASE airwaze_test;
```

Now we want to install the geospatial extensions to Postgres for the `airwaze` db. Open the Postgres UI and double click on the `airwaze` db to open a `psql` command prompt connected to the `airwaze` db:
```nohighlight
# CREATE EXTENSION postgis;
# CREATE EXTENSION postgis_topology;
# CREATE EXTENSION fuzzystrmatch;
# CREATE EXTENSION postgis_tiger_geocoder;
```

Make sure that everything installed correctly by running this query:
```nohighlight
# SELECT POSTGIS_FULL_VERSION();
```

Remember to do that for your test database as well!  

Open the Postgres UI and double click on the `airwaze_test` db to open a `psql` command prompt connected to the `airwaze_test` db.
```nohighlight
# CREATE EXTENSION postgis;
# CREATE EXTENSION postgis_topology;
# CREATE EXTENSION fuzzystrmatch;
# CREATE EXTENSION postgis_tiger_geocoder;
# SELECT POSTGIS_FULL_VERSION();
```

Create a new user for your application. Open the Postgres UI and double click on the `airwaze` db to open a `psql` command prompt connected to the `airwaze` db.
```nohighlight
# CREATE USER airwaze_app_user WITH PASSWORD 'somethingsensible' CREATEDB;
# ALTER ROLE airwaze_app_user WITH SUPERUSER; 
```

##Now Let's Use the New Database
This application uses Spring Boot. If you have a `src/main/resources/import.sql` file, it will automatically run when the project boots up. Take a second to review `import.sql` in IntelliJ. The two `.csv` files were created from `.geojson` data.

<aside class="aside-note" markdown="1">
Be sure that the `import.sql` points to local copies of the `airports.csv` and `routes.csv`. It needs to be the "full path" to each file on your local computer.
</aside>


## Let's See It in Action
- Run the `bootRun` gradle task.
- Make sure there aren't any errors in the log.
- Then go to `http://localhost:8080`. You should see a map with Mexico on it that includes a map layer for airports as red circles.

## Review AirportController
- Visit this url in your browser: http://localhost:8080/airport/.  Note the trailing `/` is important.
- Then look at the code that returns that data.

## Review RouteController
- Visit this url in your browser : http://localhost:8080/route/?srcId=12 .
- Then look at the code that returns that data.
- Think about how this route could be used to show the routes for an airport.  

## Tasks

* When an airport is clicked on the map, show the routes for that airport.
* This will require you to add a new layer to the map. Refer to the "airports layer" for help.
* You will need to add more code to the function `map.forEachFeatureAtPixel(event.pixel, function(feature,layer)` in `resources/static/js/scripts.js`
* The data for the new layer will be provided by `http://localhost:8080/route?srcId=X`, where X will be the `airportId` from the feature.

## Problems

Invalid JSON will cause the jQuery callback to not fire.  If you are having an issue, validate the JSON that you are sending across the wire.  Here is a [JSON Validator](https://jsonlint.com/).

Invalid GeoJSON will not show up on your map.  You will see the data pull back, but you will not see any data displayed.  Validate your GeoJSON.  Here is a [GeoJSON Validator](http://geojson.io/o).

If you get the message `The element type "link" must be terminated by the matching end-tag "</link>".`, then add the following tag `<html lang="en" xmlns:th="http://www.thymeleaf.org/">`.

## Resources
- [Read about constructing GeoJSON](https://macwright.org/2015/03/23/geojson-second-bite)
- [OpenLayers Examples](https://openlayers.org/en/latest/examples/)
- [Adding and Removing Layers](http://www.acuriousanimal.com/thebookofopenlayers3/chapter02_01_adding_removing_layers.html)
- [Validate your GeoJSON!](http://geojson.io)
- [OpenLayers Drawing Examples](http://openlayers.org/en/latest/examples/geojson.html)
