---
title: "Studio: Airwaze"
---

Your goal is to pull additional about flight routes and display it on the map. Once you have sourced additional flight data, allow the user to click on an airport to show all of the flights departing from that city.

## Setup

<aside class="aside-note" markdown="1">
Throughout these instructions, commands that should be run in a terminal have `$` at the beginning of the line, while commands run in `psql` have `#` at the beginning of the line. These signify the different command prompts.
</aside>

### PostGIS

To install PostGIS, run the following commands:

```nohighlight
$ brew update
$ brew install postgis
```
This is going to take a while.

Once it installs, use the following command to start the PostGIS server:

```nohighlight
$ pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start
```

Create a database:

```nohighlight
$ createdb airwaze
```

Also create a database for your tests:

```nohighlight
$ createdb airwaze_test
```

You only have a Postgres database until you install all of the geospatial extensions. Run the following commands:

```nohighlight
$ psql -U YOUR_MAC_USERNAME airwaze
# CREATE EXTENSION postgis;
# CREATE EXTENSION postgis_topology;
# CREATE EXTENSION fuzzystrmatch;
# CREATE EXTENSION postgis_tiger_geocoder;
```

<aside class="aside-note" markdown="1">
Remember to use your admin account, which is your mac user name.
</aside>

Make sure that everything installed correctly:

```nohighlight
# SELECT POSTGIS_FULL_VERSION();
```

Remember to do that for your test database as well:

```nohighlight
$ psql -U YOUR_MAC_USERNAME airwaze_test
# CREATE EXTENSION postgis;
# CREATE EXTENSION postgis_topology;
# CREATE EXTENSION fuzzystrmatch;
# CREATE EXTENSION postgis_tiger_geocoder;
```

Make sure that everything installed correctly:

```nohighlight
# SELECT POSTGIS_FULL_VERSION();
```

Exit `psql` (using `\q`) and create a new user `airwaze` for your application:

```nohighlight
$ createuser airwaze --createdb
```

Make this user a super user. Normally this would be a security risk, however we're only working locally so doing this is acceptable.

```nohighlight
# ALTER ROLE airwaze WITH SUPERUSER;
```

Log in with app_user and change the password to something sensible
```nohighlight
$ psql -U airwaze airwaze
# \password
```

Now with you your super user postgres account (the one that matches your Mac username), log in and run the following commands to install all of the geospatial extensions to PostGIS:
```nohighlight
# CREATE EXTENSION postgis;
# CREATE EXTENSION postgis_topology;
# CREATE EXTENSION fuzzystrmatch;
# CREATE EXTENSION postgis_tiger_geocoder;
```

Make sure that everything installed correctly:
```nohighlight
# SELECT POSTGIS_FULL_VERSION();
```

Be sure that the `import.sql` points to local copies of the `Airports.csv` and routes.csv. The `import.sql` command gets run upon starting up the Spring Boot server, at which point it imports the CSV files for airports and routes.

## Your Tasks

Now that have the app and database set up, dive into the following tasks:

* Use the [openflights-geojson Github repository](https://github.com/node-geojson/openflights-geojson) to pull the flight route data.
* Serve the data up from Spring Boot as static GeoJSON by placing the file in `src/resources/static/json/`).
* Using a jQuery AJAX call, pull the data from the server and display the routes for a particular airport when the airport is clicked.

At this point, you may [turn in your work](#turning-in-your-work) before continuing on to the Bonus Mission.

## Bonus Mission

Create a dynamic web service in Spring Boot to serve up the route data. The URL of the service should be `/route/`. The service should allow you to query for the route by `srcId` of the airport. For example, to search for a route the URL would look like `/route?srcId=1299`.

Be sure to use the `AirportController` as an example. Also, you will need to use `@RequestParam` to pass in the `SrcId` variable. Here's an [article](https://reversecoding.net/spring-mvc-requestparam-binding-request-parameters/) if you're unfamiliar with `@RequestParam`.

- [Read about constructing GeoJSON](https://macwright.org/2015/03/23/geojson-second-bite)
- [OpenLayers Examples](https://openlayers.org/en/latest/examples/)
- [Adding and Removing Layers](http://www.acuriousanimal.com/thebookofopenlayers3/chapter02_01_adding_removing_layers.html)
- [Validate your GeoJSON!](http://geojson.io)
- [OpenLayers Drawing Examples](http://openlayers.org/en/latest/examples/geojson.html)

## Common Problems

- Invalid JSON will cause the jQuery callback to not fire. If you are having an issue, validate the JSON that you are sending back from the server. Here is a [JSON Validator](https://jsonlint.com/)

- Invalid GeoJSON will not show up on your map. You will see the data pull back, but you will not see any data displayed. Validate your GeoJSON. Here is a [GeoJSON Validator](http://geojson.io/o)

- If you get the message `The element type "link" must be terminated by the matching end-tag "</link>"`, then add the following tag:
    ```html
    <html lang="en" xmlns:th="http://www.thymeleaf.org/">
    ```

## Turning In Your Work

If you don't complete each of the tasks, turn in as much as you have completed by the end of the day.

- Commit and push your work to GitLab
- Notify the instructor that you are done