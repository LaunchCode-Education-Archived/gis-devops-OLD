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

Create `env.list`

```nohighlight
$ docker run --name "postgis" -p 5433:5432 -d -t --env-file ./env.list kartoza/postgis:9.4-2.1
```

```nohighlight
$ docker run --name "geoserver" --link postgis:postgis -p 8081:8080 -d -t kartoza/geoserver
```

<aside class="aside-warning" markdown="1">
If the `postgis` docker image is not running when starting the geoserver, the link will fail.
</aside>


docker run --name "elasticsearch" -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:5.6.0