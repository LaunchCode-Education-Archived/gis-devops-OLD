---
title: "Working in Docker"
---

In this studio, you will be using Docker and Docker Compose to run the **Airwaze** web app.  By setting up Docker and Docker Compose you enable others to easily run your app.

## Resources

In this studio, you will be relying on the Docker and Docker Compose documentation.  Be sure to ask questions when you don't understand something, but try to use the docs as much as possible.  Here are some helpful resources:

* [Docker and Docker Compose Cheatsheet](https://devhints.io/docker-compose)
* [Official Docker Compose Docs](https://docs.docker.com/compose)

<aside class="aside-warning" markdown="1">
  Remember that the `--link` flag is deprecated for Docker. Please use Docker Compose instead.  :-)
</aside>

### Helpful Commands

* `sudo docker logs {container name}` show all of the logs written to `STDOUT`.
* `sudo docker exec -it {container name}` access to the command line inside the container.

## Goals

At the end of this studio your project should have the following:
1. A Docker file that runs the Airwaze executable jar file. 
2. A Docker Compose file that allows you to stand up Airwaze with the command `docker-compose up` 

## Setup

To run Airwaze, you need two different containers:
1. A Web server running with Java installed to run a Spring Boot executable jar file.
2. A PostGIS database.


You will need to create a `Dockerfile` to run the Airwaze app.  Additionally, you will need a `docker-compose.yml` to connect the Airwaze container to the `postgis` container.  Use existing Docker images to run PostGIS.  Here are some Docker commands that we have used in the past to run PostGIS.

For running **Postgis**, we used the following docker command:
```
docker run --name "postgis" -p 5432:5432 -d -t --env-file ./env.list kartoza/postgis:10.0-2.4
```

## Hints

If you expose the port `5432` on your PostGIS container using the `-p 5432:5432` option, you will only be able to connect to it via`psql -h 127.0.0.1 -p 5432 -U airwaze_app_user airwaze`

Settings up databases in Docker can be kind of tricky because they typically require setting up users and a schema before they can be used by an application server. Here is an example `docker-compose` file that configure the PostGIS container, note all of the file is not shown.

```
  postgis:
    image: "kartoza/postgis:10.0-2.4"
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_PASS=verysecurepassword
      - POSTGRES_USER=airwaze_app_user
      - POSTGRES_DBNAME=airwaze
    command: sh -c "echo \"host all all 0.0.0.0/0 md5\" >> /etc/postgresql/10/main/pg_hba.conf && /start-postgis.sh"
```

## Bonus Missions
Get your **Zika CDC Dashboard** to run via a `docker-compose.yml` file.
- Container running **Zika CDC Dashboard**
- Container running **PostGIS**
- Conatiner running **ElasticSearch**
- Conatiner runninng **GeoServer**
- DockerFile to create the **Zika CDC Dashboard** image
- DockerFile to create and populate the **PostGIS** container 
- Docker compose file that spins up and connects the containers

## Zika Hints
For running **Elasticsearch**, we used the following docker command:
```
docker run --name "es" -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:5.6.0
```
