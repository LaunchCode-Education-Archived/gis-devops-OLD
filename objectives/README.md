---
title: Learning Objectives
currentMenu: objectives
---

## Week 1

### Day 1

- Use Git for version control
- Navigate and use GitLab
- Effectively use IntelliJ to streamline Java application development
    - Configure IntelliJ projects, including assigning the right JDK
    - Run console and web projects in IntelliJ
    - Understand Java project structure
- Improve applications by refactoring code
- Describe the purpose of unit testing, and the qualities of a good unit test
- Create unit tests in Java using JUnit

### Day 2

- Understand Agile as a guiding philosophy for rapidly deploying working code with additional value
- Describe the purpose and structure of common Agile/Scrum ceremonies: standups, planning, sprint review, retrospective
- Describe the purpose and goals of Test-Driven Development (TDD)
- Use TDD to write Java methods
- Follow the Red-Green-Refactor workflow to improve test-driven coding

### Day 3

- Understand what an integration test is compared to a unit test
- Write integration tests in Spring using the MockMvc class and associated utilities
- Exercise common MVC integration test patterns to verify return codes, response content, header content
- Understand how dependency injection works within Spring Boot
- Use @Autowired along with Spring component annotations (@Controller, @Repository, etc) to enable management and injection of components

### Day 4

- Install and use PostgreSQL via the `psql` CLI
- Write common SQL commands in PostgreSQL: select, insert, update, delete
- Understand relational database components: databases, schemas, tables, columns, constraints
- Understand the benefits of using schemas
- Use application.properties settings to configure a database connection in Spring Boot
- Understand how Spring Data, JPA, and Hibernate relate to each other

### Day 5

- Understand the structure of HTTP requests and responses, including differences based on request type (GET, PUT, POST, HEAD, DELETE)
- Understand common HTTP status codes
- Understand JSON syntax
- User cURL to make HTTP requests
- Understand what an API is, and how they are commonly used
- Understand the structure of GeoJSON
- Understand geometry types: Point, LineString, Polygon, MultiPolygon
- Understand the data provided by a WMS service using GetCapabilities and GetMap
- Create map and layer objects in OpenLayers
- Make AJAX HTTP requests using jQuery

## Week 2

Utilize the skills learned in week 1 to build a Spring Boot application that uses OpenLayers to display geospatial data on a map. Deliver an app with the the following features:

- Ingestion of geospatial data via CSV.
- Display Zika infection data on a map using OpenLayers.
- Display information about each indvidual feature.

## Week 3

### Day 1

- Describe the main features of a RESTful web service
- Describe the usage of HTTP methods in a RESTful web service
- Describe the URL format for a RESTful web service
- Describe HTTP status code usage in REST
- Explain what a resource is
- Explain how resource formats related to requests
- Explain how content negotiation works, and which HTTP headers are necessary for this
- Explain idempotence in REST
- Explain statelessness in REST
- Use and design RESTful URLs, including nested resources and query/filtering parameters

### Day 2

- Identify the difference between Swagger toolset and the Open API Specification
- Compose Swagger YAML files to define the endpoints, responses, and schema of an API
- Use `$ref` to reference reuseable definitions
- Integrate SwaggerUI into a project
- Explain the difference between authentication and authorization
- At a high level, explain how authentication and authorization work for APIs
- Explain HATEOAS from the perspective of the data returned by a REST service
- Explain the four levels of the REST maturity model

### Day 3

- Describe the use cases for Elasticsearch (ES)
- Understand how NoSQL databases structure data, in contrast to relational databases
- Describe the representation of data in ES as indexes of documents with fields
- Describe the high-level architecture of ES as being based on a cluster with nodes and shards
- Describe how ES is fault-tolerant
- Know when ES should be used beyond the primary data store for an application
- Use curl to query the search API of an index
- Write filter queries
- Understand query and filter context, and how each affects a result set
- Describe how analyzers are used for full text queries
- Describe how boost and highlighting can customize result sets
- Use pagination of result sets
- Describe and use fuzzy queries, geo queries, and aggregations

### Day 4

- Understand how parent/child relationships are represented, and how this contrasts with such relationships in relational databases
- Describe and configure document mappings, and know the causes of and preventions for mapping explosion
- Describe the purpose and procedure for reindexing
- Integrate Elasticsearch into a Spring Boot application

### Day 5

- Understand the origins of JavaScript and the ECMAScript specification
- Understand both client and server JS runtime environments
- Understand what a transpiler is, and how it enables use of different versions of JS in different environments
- Understand the benefits of linting code
- Use ESLint to ensure JS code adheres to a set of standards
- Understand and use ES2015 additions: `let`, `const`, template strings, arrow functions, default parameter values
- Understand and use Webpack to build static client-side applications

## Week 4

- Use the REST, Elasticsearch, and JavaScript skills obtained in week 3 within a student-built application.

## Week 5

### Day 1

- Use and configure SSH to access remote machines
- Manage Unix file permissions for owners and groups
- Manage Unix processes
- Configure systemd daemon processes to run on startup
- Use logs to troubleshoot applications

### Day 2

- Understand the role of the VPC in providing security for multiple instances
- Understand why AWS provides "High Availability" ELB and RDS instances
- Create ELB instances that distribute traffic across multiple EC2 servers
- Configure an EC2 instances to connect to an RDS database
- Use Telnet to troubleshoot TCP connections

### Day 3

- Understand why the 12 Factor App principles are important in building a Cloud Native app
- Explain why an ephemeral file system is required to scale apps on the cloud
- Understand how to handle log files on the cloud
- Understand the importance of parity between development, staging, and production environments
- Create an autoscaling app on AWS
- Describe why ELB and RDS databases are "high availability"

### Day 4

- Understand the purpose of Gradle, and the types of tasks it can carry out
- Describe the historical relationship between Gradle, Maven, Ivy, and Ant
- Understand the content of Gradle files as written in Groovy and the Gradle DSL
- Understand Gradle Java project structure
- Describe the three task lifecycle phases
- Recognize tasks as objects with associated behaviors
- Create basic tasks, including tasks with dependencies
- Understand that tasks can be built from provided task classes such as `DefaultTask`, `Copy`, `Jar`, and so on
- Describe the types of behavior that plugins can provide to a project
- Install and use plugins
- Understand how to configure project dependencies with proper scope
- Describe how Gradle resolves task and project dependencies using a directed acyclic graph representation
- Understand the concepts: Continuous Integration and Continuous Delivery
- Install Jenkins
- Create and configure Projects in Jenkins
- Make Projects that trigger other Projects
- Reuse the same workspace for multiple Projects
- Use Git polling to trigger a Jenkins Project to run
- Configure Jenkins to run and show results of tests
- Create a Jenkins Project to deliver the build artifact (.jar file)

### Day 5

- Understand the concept of Continuous Inspection
- Install Sonarqube
- Configure `build.gradle` to use Sonarqube
- Configure project name for Sonarqube Gradle task
- How to create a project in Sonarqube
- How to read results in Sonarqube UI

## Week 6

- Use the AWS skills learned in the previous week to deploy a cloud-hosted, scalable application to AWS

## Week 7

<aside class="aside-note" markdown="1">
GeoServer training is delivered by Boundless.
</aside>

### SU 101 Spatial Basics

- Gain a basic understanding of spatial concepts, mapping, open source, open data, data formats, geospatial concepts, and cartography.

### GS101 Data Publishing

- Publish simple datasets in GeoServer
- Accessing published data via WMS and WFS.
- Understand basic spatial file formats
- Read and configure files in the GeoServer web interface.

### SU102 Spatial Web Services

- Gain a basic understanding of web service concepts
- Demonstrate working knowledge of Web Map Service, Web Feature Service and OGC standards.

### GS102 Administration

- Demonstrate GeoServer management, specifically the web administration interface.
- Be able to configure individual web services, manage the security system.
- Apply basic troubleshooting techniques.

### GS103 Data Management

- Apply tools tools to manipulate data to resolve issues of performance or data security.
- Recognize more advanced store types which GeoServer supports and how and why a GeoServer administrator would select these to serve their spatial data.

### GW101 GeoWebCache

- Discuss and explain concepts behind GeoWebCache as a specialized type of web cache and understanding how it can be configured to function as a component of a GeoServer instance in production.
- Demonstrate basic configuration.

### PG101 Introduction to Spatial Databases

- Gain a basic understanding of spatial databases, competing technologies, application and use.
- Explain value of PostGIS with capabilities, history and success stories.
- Demonstrate basis skills such as creating a PostGIS database, connecting to a database from QGIS and GeoServer.

### PG102 PostGIS Explained

- Demonstrate knowledge of geometry use in a PostGIS.
- Apply skills to import and export data.
- Describe, explain and apply basic SQL knowledge.

### PG103 PostGIS Explored

- Demonstrate SQL knowledge in applied queries
- Apply spatial joins, spatial indexes.
- Demonstrate Knowledge of projects and apply knowledge to effectively work with data.
- Represent 3D data.
- Apply linear referencing.
- Load raster data into a database.
- Load a road network into PgRouting.
- Gain a basic understanding of point cloud data.

### PG 104 PostGIS Analysis

- Demonstrate proficient knowledge of SQL for spatial analysis.
- Demonstrate proficient knowledge of spatial joins.
- Explain DIM-9 Spatial relationship optimization.
- Apply nearest neighbor analysis.
- Apply raster analysis.
- Apply topology relationships through SQL.

## Week 8

- Use the skills learned in the previous week to integrate GeoServer with a Spring Boot + OpenLayers application, both locally and on AWS

## Week 9

### Days 1-2

<aside class="aside-note" markdown="1">
Pivotal Cloud Foundry training is delivered by Boundless.
</aside>

- PCF architecture
- How to interact with PCF: Command Line Interface (CLI), Apps Manager UI
- Orgs, spaces, user roles
- Deploy a Simple Application
- Scaling an app (Ver / Hor)
- Buildpacks
- Application Manifests
- Domains and Routes
- Logging and Metrics
- Application Monitoring
- Blue/Green App Deployment
- Services Marketplace
- Create & Bind a Service
- Platform Security
- NGA’s PCF envs

### Day 3

- Understand how Docker differs from traditional VMs.
- Describe the underlying Docker technologies such as Linux Containers and UnionFS.
- Spin up containers from existing images locally mapped ports.
- Spin up containers with both volumes and write through mounts.
- Create a Dockerfile that is capable of running a SpringBoot server.
- Understand Docker Network and how Docker containers are interconnected.
- Ability to create, inspect, and delete both images and containers.
- Create a Docker Compose config to spin up a web app, database, and Elasticsearch instance.

### Day 4

- Understand the difference between authentication and authorization
- Understand OAuth roles: resource owner, client, resource server, authorization server
- Know how to register an application
- Understand the general OAuth2 flow
- Understand the roles of cliend ID and client secret
- Understand OAuth authorization parameters: endpoint, client ID, redirect URI, response type, scope
- Understand the role of an access token in the authorization flow
- Understand the four OAuth grant types: auth code, implicit, resource owner password credentials, client credentials
- Understand the refresh token flow

### Day 5

- Understand the role certificates play in validating the identity of a server.
- Understand the role that a certificate authority plays in determining trust.
- Configure the browser to add new trusted certificates.
- Configure the browser to add client-side access certificates.
