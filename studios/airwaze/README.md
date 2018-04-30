---
title: "Studio: Airwazes"
---

Your goal is to add an additional layer that shows flight routes and display it on the map.  This time we will incorporate our map into a Spring Boot project. Some starter code has been provided for you.

##Set Up Project
- Fork and clone this [Gitlab project](https://gitlab.com/LaunchCodeTraining/airwaze-studio).
- Then open the project in IntelliJ
- Create a feature branch `$ git checkout -b day5-solution`

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

Remember to do that for your test database `airwaze_test` as well!

## Create Users and Grant Access Rights
- Create users `airwaze_user` and `airwaze_test_user`
- Grant the users access to only what they need `airwaze_user` -> `airwaze` and `airwaze_test_user` -> `airewaze_test`

##Now Let's Use the New Database
Notice in `application.properties` and `application-test.properties` that the db configurtion refers to token values like `${APP_DB_NAME}` and `${APP_DB_HOST}`.
- Define each in environmment variables OR in Intellij run configurations
- Do not commit db config information to source control

## Seed Data
This application uses Spring Boot. If you have a `src/main/resources/import.sql` file, it will automatically run when the project boots up. Take a second to review `import.sql` in IntelliJ. The two `.csv` files were created from `.geojson` data.
- Review the `import.sql` file in the airwaze project
- Open the `csv` files to see what data is being imported for routes and airports

<aside class="aside-note" markdown="1">
Be sure that the `import.sql` points to local copies of the `airports.csv` and `routes.csv`. It needs to be the "full path" to each file on your local computer.
</aside>

## Let's See It in Action
- Run the `bootRun` gradle task.
- Make sure there aren't any errors in the log.
- Then go to `http://localhost:8080`. You should see a map with Mexico on it that includes a map layer for airports as red circles.

## Review the Created Tables and Imported Data in Postgis
Open a `psql` prompt connected to `airwaze` database and then run these commands one at a time
```sql
airwaze=# select count(*) from route;
airwaze=# select count(*) from airport;
airwaze=# \d
airwaze=# \d airport
airwaze=# \d route
```

## Review AirportController
- Visit this url in your browser: http://localhost:8080/airport/.  Note the trailing `/` is important.
- Then look at the code that returns that data.

## Tasks
1. When the map is clicked, list all airports that are at that pixel
 * You will need to add more code to the function `map.forEachFeatureAtPixel(event.pixel, function(feature,layer)` in `resources/static/js/scripts.js`
2. Create a route endpoint that returns routes for a certain srcId. Example: `http://localhost:8080/route/?srcId=12`
3. When an airport feature is clicked on the map, show the routes for that airport
 * By adding a router layer that only contains routes connected to the clicked airport
 * The data for the new layer will be provided by `http://localhost:8080/route/?srcId=X`, where X will be the `airportId` from the feature. 
4. Write tests for all of this. Tests are your friend :) 

## Bonus Missions
- Get this to work without jQuery. Hints: `fetch`([info here](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)) and `document.findElementById`
- Change the style of the dots: color, size, fill in
- Sort airports by alpha order when they are displayed below the map
- Check what kind of feature was clicked when map.onclick runs
- Remove previous route layers when adding a new one

## Resources
- [Read about constructing GeoJSON](https://macwright.org/2015/03/23/geojson-second-bite)
- [OpenLayers Examples](https://openlayers.org/en/latest/examples/)
- [Adding and Removing Layers with OpenLayers](http://www.acuriousanimal.com/thebookofopenlayers3/chapter02_01_adding_removing_layers.html)
- [Validate your GeoJSON!](http://geojson.io)
- [OpenLayers Drawing Examples](http://openlayers.org/en/latest/examples/geojson.html)
