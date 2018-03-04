---
title: Week 8 - Project Week
---

- [Overview](#overview)
- [Setup](#setup)
  - [Create Docker containers](#create-docker-containers)
  - [Populate PostGIS database](#populate-postgis-database)
- [Requirements](#requirements)

## Overview

Read [Mission Briefing 4](../../materials/week06/zika_mission_briefing_4.pdf).

You will be adding GeoServer to your local and deployed application. This will require several steps, including setting up a GeoServer instance, connecting it to a PostGIS store, and creating a new layer from existing data.

After you have this new system fully set up, you'll be able to add additional layers to GeoServer to view temperature and elevation data.

## Setup

Before getting started, be sure you don't have your Boundless virtual machine running. We'll be using the same ports as the VM, so if it is running there will be conflicts.

### Create Docker containers

Create a file `env.list` in the root of your `zika-cdc-dashboard` project with the same contents as [our `envlist` file](https://gist.github.com/chrisbay/d74442a8e8707111472a742832d76796).

```nohighlight
$ docker run --name "postgis" -p 5432:5432 -d -t --env-file ./env.list kartoza/postgis:9.4-2.1
```

<aside class="aside-warning" markdown="1">
In `env.list` you'll see that the `POSTGRES_DBNAME` environment variable is set to `zika`. This variable is supposed to set the name of our PostGIS-enabled database within the container to be `zika`. However, a bug in the Dockerfile for this image ignores the name, creating a database named `gis`.
</aside>

```nohighlight
$ docker run --name "geoserver" --link postgis:postgis -p 8080:8080 -d -t kartoza/geoserver
```

<aside class="aside-warning" markdown="1">
If the `postgis` docker image is not running when starting the geoserver, the link will fail.
</aside>

<aside class="aside-note" markdown="1">
When it's container is running, you can access this GeoServer instance the same way in which you previously accessed GeoServer locally when running the Boundless virtual machine. It will be running on port 8080 (try http://localhost:8080/geoserver) with credientials **admin / geoserver**.
</aside>

If you don't have an Elasticsearch container set up already, create one now:

```nohighlight
$ docker run --name "es" -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:5.6.0
```

<aside class="aside-warning" markdown="1">
If Docker has no more than 2G of memory allocated for container use, you may have issues with the `elasticsearch` container crashing due to lack of memory. If this happens, increase memorgy to at least 3G by going to *Docker > Preferences > Advanced*.
</aside>

### Populate PostGIS database

We need to put our data files on the server. First, let's change the paths referenced in the file to `'/tmp/locations.csv'` and `'/tmp/all_reports.csv'`.

Then copy the file to the `postgis` contianer:

```nohighlight
$ docker cp locations.sql postgis:/tmp
$ docker cp all_reports.sql postgis:/tmp
```

Verify that the files made it:

```nohighlight
$ docker exec -it postgis ls -l /tmp
```

The `data.sql` file that will be run on the `postgis` container will need to find the data files in `/tmp`, so update that script accordingly (recall that `data.sql` is run on the database host, which is the `postgis` container, in this case).

Additionally, the script makes use of the `unaccent` function, which is part of the `unaccent` Postgres extension. While our Docker image came with the PostGIS extension installed, the `unaccent` extention is *not* present. Let's fix that.

Fire up `psql`:

```nohighlight
$ psql -h localhost -p 5432 -U zika_app_user -d gis
```

And then install the extension:

```nohighlight
# create extension unaccent;
```

Exit `psql`.

Now, configure your `zika-cdc-dashboard` app so it can connect to the PostGIS datbase. This requires editing the environment variables in the `Application` run configuration. The only edit you should need to make is to set the `APP_DB_NAME` to `gis` (see the Warning above).

Before we can run our Spring app, we need to configure it to run on a port other than 8080. Recall that we set up the GeoServer container to bind to port 8080 on our localhost, so the default for Spring (which is also 8080) will not work. We can easily adjust the port that Spring will run on by adding `server.port=8000` to `application.properties`.

Start up your Spring app. Verify that the app started up cleanly, and that the `locations` and `reports` databases were built and populated properly.

<aside class="aside-pro-tip" markdown="1">
If your `locations` and `reports` databases aren't being populated, you can populat them manually by copying the `data.sql` file in `src/main/resources/` to the `postgis` container (see above) and running:

```nohighlight
$ docker exec -it postgis psql -h localhost -U zika_app_user -d gis -a -f /tmp/data.sql
```
</aside>

## Requirements

- Create workspace (lc/https://launchcode.org)
- Create data store (w/ database gis)
  - Note: use postgis as hostname
- Create layer from `location` table
- Preview the layer
