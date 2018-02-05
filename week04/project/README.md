---
title: Week 4 - Project Week
---

## Project

[Mission Briefing 2](../../materials/week04/zika_mission_briefing-mission_2.pdf)

## Overview

Your goal is to extend the CDC Zika Dashboard that you built last week. The scientists need several new features for the dashboard. Here are the stories:

1. CDC scientists have the ability to add new reports to the database.
2. CDC scientists have the  ability to visualize how the infection rate of each area changes over time.
3. CDC scientists have the ability to perform fuzzy search across all of the data.

<aside class="aside-note" markdown="1">
Remember, in Agile a story is just a "guaranteed conversation". Stories usually don't contain all of the details necessary to complete the task and that's why it is important to follow up with the client and talk through the exact needs of your customer.
</aside>

## Getting the code

Go to the [zika-cdc-dashboard](https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard) directory in Terminal and check out the `week4-starter` branch.

## Breaking down the stories

*CDC scientists want to be able to add new reports to the database.*

1. Add geometry boundaries to the database.
2. Create a new REST endpoint for adding reports to the (Postgres) database.
3. When saving a new report to the main repository/db, also save it to the Elasticsearch document repository.
4. Add Springfox to generate Swagger documentation.

## Setup

Install the following extension on your database:

```nohighlight
$ psql -U zika_app_user zika
# CREATE EXTENSION unaccent;
```

### Adding Boundary Geometries

The data that the scientists want to ingest is summarized in the [CDC Zika Repository](https://github.com/cdcepi/zika). If you open up the [data for Argentina](https://github.com/cdcepi/zika/blob/master/Argentina/Surveillance_Bulletin/data/Surveillance_Bulletin_01_2017-01-12.csv), you'll notice that the data looks pretty similiar to last mission, except that there is no latitude or longitude to GeoCode each row; however, each row does have a location field. We should be able to indentify those locations to actual data points on a map.

You Google for "political boundaries geojson" and you find [gadm.org](http://www.gadm.org/). [gadm.org](http://www.gadm.org/country) serves up geospatial data about administrative boundaries for each state. Go to the [GADM Downloads Page](http://www.gadm.org/country) to check out the data. 

<img src="../../materials/week04/GADM_download_page.png"></img>

[Download the shapefile for Brazil](http://biogeo.ucdavis.edu/data/gadm2.8/shp/BRA_adm_shp.zip).

The file `BRA_adm_shp.zip` will download. Double click the file to unzip the file. You should see three shapefiles: `BRA_adm0.shp`, `BRA_adm1.shp`, `BRA_adm2.shp`. `BRA_adm3.shp`. Let's take a look at these shapefiles. In order to look at a shapefile, you will need download [QGIS](https://qgis.org/en/site/), an open source desktop viewer for geospatial data. [Click Here to Download QGIS](https://connect.boundlessgeo.com/Downloads). Double click the `.dmg` file to install.

<aside class="aside-note" markdown="1">
  Note: Use your personal email to register on Boundless Connect to get access to the QGIS download.
</aside>

After QGIS is installed, drag the `BRA_adm1.shp` file into the QGIS window in order to import the file. 

<aside class="aside-note" markdown="1">
  The zoom on the QGIS window is VERY sensitive. You may need to automatically zoom to the layer you would like to view. Right click on your layer in the `Layers Panel`, and select `Zoom to Layer`.
  <img src="../../materials/week04/QGIS_zoom_to_layer.png"></img>
</aside>

Great! That looks exactly like what we need. Let's convert the file into GeoJSON so that we can serve it up from within our web application. We can use the `ogr2ogr` command.

```nohighlight
$ ogr2ogr -f "GeoJSON" brazil.geojson BRA_adm_shp/BRA_adm1.shp
```

After the command completes, check out the `brazil.geojson` file. Yikes! The file seems pretty big. Let's check how big:

```nohighlight
$ ls -lh brazil.geojson
```

<img src="../../materials/week04/CLI_check_file_size.png"></img>

Oh man... a 25M file is not going to work well in our web app. And that's just Brazil!!! 

Fortunately, shapefiles can be compressed in size by reducing the amount of detail. In QGIS, select `Vector > Geometry Tools > Simplify geometries` from the top menu. Select your Brazil Geometry `BRA_adm1` and set the tolerance to `0.05`. Hit Run.

<img src="../../materials/week04/QGIS_simplify_geometries.png"></img>

QGIS should generate a new layer that looks pretty much the same as the last layer.

Right click on the newly created layer and select "Save As...". Save the file as GeoJSON with the name `brazil_compressed.geojson`. Be sure to type in the entire path of the file that you are creating.

<img src="../../materials/week04/QGIS_save_as.png"></img>
Now if you check the size of the newly created `brazil_compressed.geojson`, you should see that it is much smaller!

Run the command:

```nohighlight
$ ls -lh brazil_compressed.geojson
```

<img src="../../materials/week04/CLI_check_compressed_file_size.png"></img>

<aside class="aside-note" markdown="1">
  So 331K isn't actually that great for a webapp... it's still a bit large. In a few weeks, we'll look at how some of the features of GeoServer allows you to display large amounts of data without a big download. 
</aside>

Now we have to Repeat this process for all of the countries listed in [Zika Github Repository](https://github.com/cdcepi/zika) including Argentina, Columbia, Dominican Republic, Ecuador, El Salvador, Guatamala, Haiti, Mexico, Nicaragua, and Panama.

To save you time, we went ahead and optimized the geometries for all of these countries. They are listed in [/data/optimized](https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/tree/week2-starter).

The last step is to join all of the GeoJSON files together. To do that, we can use a nice Node.js library from MapBox. Run the following commands:

```nohighlight
$ npm install -g @mapbox/geojson-merge
$ geojson-merge argentina_compressed.geojson brazil_compressed.geojson columbia_compressed.geojson dominican_republic_compressed.geojson el_salvador_compressed.geojson equador_compressed.geojson guatamala_compressed.geojson haiti_compressed.geojson mexico_compressed.geojson
nicaragua_compressed.geojson panama_compressed.geojson > states.geojson
```

Place the `states.geojson` file in the `src/main/resources/static/json` so that it can be statically served up in your web app under the `http://localhost:8080/json/states.geojson` path.

#### Adding new Reports

After we talk to the scientists, we find out that they want the ability for reports to be automatically ingested into the database. You decide that you are going to build a RESTful API. Specifically, the API needs to have the following:
1. An integration test
2. An endpoint that accepts a POST request and JSON.
3. Swagger documentation that describes the endpoint and how the JSON is structured.

## Bonus Missions

If you complete the studio early, here are some bonus missions to go after:
1. Add ESLint to your project and ensure that all of your JavaScript meets the standards defined by the AirBNB standards. (Hint: Review the instructions for the [ESLint Airwaze Walkthrough](../../walkthroughs/eslint-airwaze))
