---
title: Week 6 - Project Week
---

## Project

[Mission Briefing 3](../../materials/week06/zika_mission_briefing_3.pdf)

## Overview

The Zika Dashboard that your team has built is growing in popularity.  In fact, the application is strained under increased load from CDC Scientists. Your team needs to find a way for you app to scale under increased load.  The goals of this mission are as follows:

1. Deploy your application remotely using Amazon Web Services.
2. Configure a Jenkins job to automatically build and test your code.  Jenkins should save an executable JAR file to S3 if all of the following checks pass:

## Requirements

To complete this project, your app should meet the following requirements:
  * Your application is deployed via AWS at a live URL.
  * Your application can consistently handle 300 requests per second.
  * Bonus:  Jenkins watches your repo pushes a release to S3 if all of the following pass:
    * All JUnit tests pass.
    * ESLint has no warnings.  

### A Couple of Notes 
There will be a few differences in this project compared to previous week's studios:
1. You will be using the `CentOS` database.  `CentOS` is a free, enterprise class, Linux distribution based on Red Hat Enterprise Linux. Most of the commands will be the same as Ubuntu, except the package manager will use `yum install` instead of `apt-get install`. Here is the [URL to the AWS Image of CentOS](https://wiki.centos.org/Cloud/AWS)
2. You will be working in `us-west-2` region (also known as `Oregon`).
3. The project has been upgraded to Gradle 4.4.  You'll want to use your existing project, but Feel free to merge in changes from the [week6-starter branch](https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/tree/week6-starter). Use the command `gradle clean bootJar` to build your project.
4. You will be running ElasticSearch remotely in this project.  You'll need to spin up a `t2.small` EC2 instance to serve ElasticSearch.  Use the [`startup_elasticsearch.sh` script](https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week6-starter/cloud/elastic_userdata.sh) in the week6-starter project to configure a `t2.medium` machine. If you get an "Out of Memory Exception", be sure to increase the heap size by setting `Xms3g` and `Xmx4g` in the `/etc/elasticsearch/jvm.options` file.

To run Elasticsearch locally, we are going to be using Docker, here is the command:
```
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:5.6.0
```
<aside class="aside-note" markdown="1">
  Be sure to stop your home brew Elasticsearch by running `brew services stop elasticsearch`
</aside>



Deploy whatever app you currently have working.  For example, if you have ElasticSearch working be sure to spin up an ElasticSearch instance on AWS.

<aside class="aside-note" markdown="1">
  To log into a CentOS instance, you will need to use the username `centos`.
</aside>

## Setup

To get started, you are provided with a CloudFormation template in an S3 bucket. This CloudFormation template provisions a VPC with the following:
1. Two public subnets with an internet gateway (each in their own availability zone). 
2. Two private subnets (each in their own availability zone).
3. One security group for web servers (ports 80 and 22 open). `WebAppSecurityGroup`
4. One security group for databases (port 5432 open). `DatabaseSecurityGroup`
5. One security group for load balancers (port 80 open). `ELBSecurityGroup`

Here is the link to the setup CloudFormation script:
https://s3.amazonaws.com/launchcode-gisdevops-cloudformation/zika_cloudformation.json

Follow the instruction from the [Scaling AWS Studio](https://education.launchcode.org/gis-devops/studios/AWS3#configure-your-vpc) to setup your VPC from a CloudFormation script.

Also, since we're working in `us-west-2`, remember we need to reconfigure your `aws-cli` so that you're working in a the `us-west-2` region.
```
$ aws configure
AWS Access Key ID [****************7S3A]: 
AWS Secret Access Key [****************hdWj]: 
Default region name [us-east-1]: us-west-2
Default output format [None]: 
```

## Getting things Working

It will probably be helpful to spin up a machine to configure your database and check that everything works. After you set up the database and check your configuration with your `t2.medium` machine, it would be good to keep the machine around to perform long running tasks like reindexing Elasticsearch (Reindexing Elasticsearch requires a machine with 4GB of memory).  Be sure to run the reindex command directly on the machine's IP address; the command will time out if sent through the load balancer. 

Here are a few steps:
1.  Spin up a `t2.medium` CentOS instance in your VPC and name it something like `AdminMachine-YourName`.
2.  Install `telnet` using Yum.  Make sure that you can telnet to your database `telnet endpoint-of-your-db 5432`.
3.  Jump into the postgres console using `psql -h endpoint-of-your-db -U masterUsername zika` (hint you may need to install the `postgresql` library via `yum`).
4.  Once in the database, create a database user for the app `CREATE USER zika WITH PASSWORD 'verysecurepassword`.  
5.  Also, once in the database install all of the PostGIS extensions (see previous studios) as well as the [unaccent Postgres extension](https://www.postgresql.org/docs/10/static/unaccent.html)
6.  Upload your `location.csv` and your `all_reports.csv` and install them in the database using a command like:
```
psql -h gary-zika-db.cq2s2klvmrfq.us-west-2.rds.amazonaws.com -d zika -U zika_app_user -c "\copy location(ID_0,ISO,NAME_0,ID_1,NAME_1,HASC_1,CCN_1,CCA_1,TYPE_1,ENGTYPE_1,NL_NAME_1,VARNAME_1,geom) from STDIN WITH DELIMITER E'\t' CSV" < locations.csv
```
7.  Use the [Elasticsearch UserData script](https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week6-starter/cloud/elastic_userdata.sh) to spin up an Elasticsearch.
8.  Upload a copy of your executable jar to the home directory of the machine.  Set all of the environment variables on the command line (i.e. `export APP_DB_NAME=zika`)
9.  If your app runs, you know that everything is setup for your to start configuring your AutoScaling groups!
