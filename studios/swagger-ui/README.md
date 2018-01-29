---
title: "Studio: SwaggerUI"
---

In this studio, we will be adding Swagger documentation to the Airwaze application.

## Getting Ready

Use the same [Airwaze](https://gitlab.com/LaunchCodeTraining/airwaze-studio) that created in the [OpenLayers Studio](https://education.launchcode.org/gis-devops/studios/airwaze/).

## Rename your API endpoints

First we want to do some cleanup.  Now that we know how Restful API endpoints are supposed to be structured, refactor your application as follows:

* Change the `/airport/` endpoint to `/api/airport`.

* Change the `/route/` endpoint to `/api/route`.

<aside class="aside-pro-tip" markdown="1">
  To follow TDD, be sure to change your test first.  Once you see your tests fail, you can confidently refactor the routes in your application.
</aside>

## Add Swagger documentation

Use the `dist` directory in the [SwaggerUI repository](https://github.com/swagger-api/swagger-ui/tree/2.x) to generate your SwaggerUI page. The swagger documentation should be displayed at `http://localhost:8080/swagger/index.html`.

## Add GeoJSON definitions

In the `definitions` section of your `swagger.yaml`, you will need to be able to account for geojson. Copy the following [gist](https://gist.github.com/idkw/fc35bb78c4bf08e47708f57b060996fe) to `dist/swagger/swagger-geojson.yaml`.  This will allow you to access the existing yaml defitions for common geometries such as Point, MultiLineString, etc.


<aside class="aside-pro-tip" markdown="1">
  In Swagger, to reference YAML in another file, use the following command `$ref: ./swagger-geojson.yaml`.  Here is a [blog post](http://azimi.me/2015/07/16/split-swagger-into-smaller-files.html) that has a full explanation.
</aside>

## Add Swagger documentation

Now that you are setup, add Swagger documentation for the `airport` and `route` endpoints in the Airwaze app.

## Turn in your Work

* Commit and push your work to GitLab.
* Notify the instructor that you are done, along with the name of the branch that you completed your work in.
