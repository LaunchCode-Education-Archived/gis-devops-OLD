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
- Integration Tests in Spring Boot

### Day 4
- Install and use postgreSQL
- Create, alter, update, delete: databases, schemas, tables, columns, contraints
- Use application.properties settings to configure a database connection in Spring Boot

### Day 5

- AJAX requests
- Open Layers

## Week 2

Deliver an app with the the following features:

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
- Use Elasticsearch.js to query ES instances from the browser

## Week 5

### Day 1

- Use and configure SSH to access remote machines.
- Manage Unix file permisions for owners and groups.
- Manage Unix processes.
- Configure systemd daemon processes to run on startup.
- Use logs to troubleshoot applications.

### Day 2

- Understand the role of the VPC in providing security for multiple instances.
- Understand why AWS provides "High Availability" ELB and RDS instances.
- Create ELB instances that distribute traffic across multiple EC2 servers.
- Configure an EC2 instances to connect to an RDS database.
- Use Telnet to troubleshoot TCP connections.

### Day 3

- Understand why the 12 Factor App principles are important in building a Cloud Native app.
- Explain why an ephemeral file system is required to scale apps on the cloud.
- Understand how to handle log files on the cloud.
- Understand the importance of parity between development, staging, and production environments.
- Create an autoscaling app on AWS.
- Describe why ELB and RDS databases are "high availability".

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
- Describe how Gradle resovles task and project dependencies using a directed acyclic graph representation
- Understand the concepts: Continuous Integration and Continuous Delivery
- Install Jenkins
- Create and configure Projects in Jenkins
- Make Projects that trigger other Projects
- Reuse the same workspace for multiple Projects
- Use Git polling to trigger a build
- Configure Jenkins to run and show results of tests
- Create a Jenkins Project to deliver the build artifact (.jar file)


### Day 5
- Understand the concept of Continous Inspection
- Install Sonarqube
- Configure build.gradle to use sonarqube
- Configure project name for sonarqube gradle task
- How to create a project in sonarqube
- How to read results in sonarqube UI

## Week 9

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

- Understand the role certificates play in validating the identity of a server.
- Undertstand the role that a Certificate Authority plays in determining trust.
- Configure the browser to add new trusted certificates.
- Configure the browser to add client-side access certificates.
