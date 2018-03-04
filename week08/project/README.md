---
title: Week 8 - Project Week
---

## Project

TODO - need project briefing

[Mission Briefing 4](../../materials/week06/zika_mission_briefing_4.pdf)

## Overview

TODO

## Requirements

TODO

- Create workspace (lc/https://launchcode.org)
- Create data store (w/ database gis)
  - Note: use postgis as hostname

## Setup

### Create Docker containers

Create `env.list`

```nohighlight
$ docker run --name "postgis" -p 5432:5432 -d -t --env-file ./env.list kartoza/postgis:9.4-2.1
```

<aside class="aside-warning" markdown="1">
TODO - note about database name not getting set correctly.
</aside>

```nohighlight
$ docker run --name "geoserver" --link postgis:postgis -p 8080:8080 -d -t kartoza/geoserver
```

<aside class="aside-warning" markdown="1">
If the `postgis` docker image is not running when starting the geoserver, the link will fail.
</aside>

If you don't have an ES container set up already, create one now:

```nohighlight
$ docker run --name "es" -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:5.6.0
```

### Populate PostGIS database

We need to put our data files on the server. First, let's change the paths referenced in the file to `'/tmp/locations.csv'` and `'/tmp/all_reports.csv'`.

Then copy the file to the `postgis` contianer:

```nohighlight
$ docker cp locations.sql postgis:/tmp
$ docker cp all_reports.sql postgis:/tmp
```

Verify that the file made it:

```nohighlight
$ docker exec -it postgis ls -l /tmp
```

The `data.sql` file that will be run on the `postgis` container will expect to find the data files in `/tmp`, so update that script accordingly (recall that `data.sql` is run on the database host). 

Additionally, the script makes use of the `unaccnet` function, which is part of the `unaccent` Postgres extension. While our Docker image came with the PostGIS extension installed, this extention is not present. Fire up `psql`:

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
