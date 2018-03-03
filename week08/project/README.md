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

## Deployment

For deployment on AWS, you will be using a `t2.small` CentOS machine.  The following UserData script should be included in the "Advanced Details" details section of "Configure Instance".  This script installs Apache Tomcat, downloads the Boundless Suite WAR, and deploys the geoserver WAR the Apache Tomcat server.  The deployed geoserver can be reached on http://{your IP}:8080/geoserver.

```
#!/bin/bash

yum update -y && yum install -y wget firewalld telnet unzip java-1.8.0-openjdk.x86_64

# Add a user for Tomcat
groupadd tomcat
mkdir /opt/tomcat
useradd -s /bin/sh -g tomcat -d /opt/tomcat tomcat
wget http://www-us.apache.org/dist/tomcat/tomcat-8/v8.5.28/bin/apache-tomcat-8.5.28.tar.gz
tar -zxvf apache-tomcat-8.5.28.tar.gz -C /opt/tomcat --strip-components=1
 
chmod g+rwx /opt/tomcat/conf
chmod -R g+r /opt/tomcat/conf
chmod g+x /opt/tomcat/conf
chmod g+rwx /opt/tomcat/bin
chmod -R g+r /opt/tomcat/bin
chown -R tomcat:tomcat /opt/tomcat

cat << EOF > /etc/systemd/system/tomcat.service
[Unit]
Description=Apache Tomcat Web Application Container
After=syslog.target network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC -XX:SoftRefLRUPolicyMSPerMB=36000 -XX:-UsePerfData -Dorg.geotools.referencing.forceXY=true -Dorg.geotoools.render.lite.scale.unitCompensation=true'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/bin/kill -15 $MAINPID

User=tomcat
Group=tomcat

[Install]
WantedBy=multi-user.target
EOF

# Open up Port 80
systemctl enable ferewalld
systemctl start firewalld
firewall-offline-cmd --zone=public --add-port=8080/tcp

systemctl daemon-reload
systemctl start tomcat
systemctl enable tomcat

# Install AWS CLI
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
unzip awscli-bundle.zip
./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws

# Install Geoserver WAR
 /usr/local/bin/aws s3 cp s3://launchcode-gisdevops-cloudformation/BoundlessServer-1.0.2-war.zip .
unzip BoundlessServer-1.0.2-war.zip 
mkdir -p /var/opt/boundless/suite/geoserver/data
unzip BoundlessServer-1.0.2-war/boundless-server-data-dir.zip -d /var/opt/boundless/suite/geoserver/data
chown tomcat:tomcat -R /var/opt/boundless/suite/geoserver/data

cat << EOF > /opt/tomcat/conf/Catalina/localhost/geoserver.xml
<Context docBase="geoserver.war">
  <!-- The location of the GeoServer configuration directory -->
  <Parameter name="GEOSERVER_DATA_DIR"
             value="/var/opt/boundless/suite/geoserver/data"
             override="false"/>

  <!-- The default location of the GWC tile cache -->
  <Parameter name="GEOWEBCACHE_CACHE_DIR"
             value="var/opt/boundless/suite/geoserver/tilecache"
             override="false"/>
</Context>
EOF
chown tomcat:tomcat /opt/tomcat/conf/Catalina/localhost/geoserver.xml
chmod 755 /opt/tomcat/conf/Catalina/localhost/geoserver.xml

mkdir -p /var/opt/boundless/suite/geoserver/tilecache
chown tomcat:tomcat -R /var/opt/boundless/suite/geoserver/tilecache

mv BoundlessServer-1.0.2-war/geoserver.war /opt/tomcat/webapps/geoserver.war
chown tomcat:tomcat /opt/tomcat/webapps/geoserver.war
chmod 755 /opt/tomcat/webapps/geoserver.war
```

The script can also be found in the [week 8 starter branch](https://gitlab.com/LaunchCodeTraining/zika-cdc-dashboard/blob/week8-starter/cloud/geoserver_userdata.sh).

<aside class="aside-hint">
	Remember the default username for Geoserver is `admin` and the default password is `Geoserver`.
</aside>
