# Studio

Your goal is to pull additional about flight routes and display it on the map.  Once you have sourced additional flight data, allow the user to click on an airport to show all of the flights departing from that city.

###Tasks
* Use the [openflights-geojson Github repository](https://github.com/node-geojson/openflights-geojson) to pull the flight route data.
* Serve the data up from SpringBoot as static GeoJSON up in SpringBoot by placing the file in `src/resources/static/json/`). 
* Using a jQuery AJAX call, pull the data from the server and display the routes for a particular airport when the airport is clicked.

###Setup
# PostGIS

To install PostGIS, run the following commands:
```
brew update
brew install postgis
```
This is going to take a while.

Once it installs, use the following command to start the PostGIS server
```pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start```

Create a database
```
createdb airwaze
```

Also create a database for your tests
```
createdb airwaze_test
```

You only have a Postgres database until you install all of the geospatial extensions.  Run the following commands:
*Remember to use your admin account (which is your mac user name)*
```
psql -U (your mac username) airwaze
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION postgis_tiger_geocoder;
```

Make sure that everything installed correctly:
```
SELECT POSTGIS_FULL_VERSION();
```

Remember to do that for your test database as well!
```
psql -U (your mac username) airwaze
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION postgis_tiger_geocoder;
```

Make sure that everything installed correctly:
```
SELECT POSTGIS_FULL_VERSION();
```

Log in with your Mac username.  If you're not sure what your mac username is then run `ls /home`
```psql -U (your username) airwaze```

Create a new user for your application
```createuser app_user --createdb```

Also, make this user a super user (normally this would be a security risk; however, we're only working locally and it is needed to prepopulate the database)
```ALTER ROLE <rolename> WITH SUPERUSER ```

Log in with app_user and change the password to something sensible
```
$ psql -U app_user airwaze
# \password
```

Now with you your super user postgres account (the one that matches your Mac username), log in and run the following commands to install all of the geospatial extensions to PostGIS:
```
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION postgis_tiger_geocoder;
```

Make sure that everything installed correctly:
```
SELECT POSTGIS_FULL_VERSION();
```

Be sure that the import.sql points to local copies of the Airports.csv and routes.csv.  The `import.sql` command gets run upon
starting up the SpringBoot server; at which point it imports the CSV files for airports and routes. 

If you need to restart your PostGIS server (or it is not running), please use the following command

# Bonus Mission
Create a dynamic web service in Spring Boot to serve up the route data.  The URL of the service should be `/route/`.  The service should allow you to query for the route by `srcId` of the airport.  For example, to search for a route the URL would look like `/route?srcId=1299`

Be sure to use the `AirportController` as an example.
Also, you will need to use `RequestParam` to pass in the `SrcId` variable.  Here's an [article](https://reversecoding.net/spring-mvc-requestparam-binding-request-parameters/) if you're unfamiliar with `@RequestParam` 


[Read about constructing GeoJSON](https://macwright.org/2015/03/23/geojson-second-bite)
[OpenLayers Examples](https://openlayers.org/en/latest/examples/)
[Adding and Removing Layers](http://www.acuriousanimal.com/thebookofopenlayers3/chapter02_01_adding_removing_layers.html)
[Validate your GeoJSON!](http://geojson.io)
[OpenLayers Drawing Examples](http://openlayers.org/en/latest/examples/geojson.html)


# Problems

Invalid JSON will cause the jQuery callback to not fire.  If you are having an issue, validate the JSON that you are sending across the wire.  Here is a [JSON Validator](https://jsonlint.com/)

Invalid GeoJSON will not show up on your map.  You will see the data pull back, but you will not see any data displayed.  Validate your GeoJSON.  Here is a [GeoJSON Validator]http://geojson.io/o

If you get the message `The element type "link" must be terminated by the matching end-tag "</link>".`, then add the following tag `<html lang="en" xmlns:th="http://www.thymeleaf.org/">`

I
