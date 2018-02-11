---
title: "Walkthrough: Intro to DevOps"
---

In this walkthrough, the instructor will show you how to work with a Unix operating system on the cloud.

## Getting Started

The instructor is going to start a machine in the cloud that we can play around with.  In this case, the machine will be on DigitalOcean, but any cloud provider will suffice.

After the machine is created, the instructor will SSH into the machine root@{ip address}.  If the instructor provided a key during the creation process, no password is necessary to login.  After logging in, the instructor will create a user for each person in the class:

```
sudo useradd -m classmate
passwd temporaryPassword
chage -d 0 classmate
```

While the instructor is creating a user for you, you need to create your own public / private key combination so that you can SSH into that machine.

Here is the command to create your keys.  By default, it will name the key id_rsa, but feel free to change that to whatever you like.
```nohighlight
$ ssh-keygen -t rsa 
Enter file in which to save the key (/home/demo/.ssh/id_rsa):     
Enter passphrase (empty for no passphrase):
Enter same passphrase again: 
Your identification has been saved in /home/demo/.ssh/id_rsa.
Your public key has been saved in /home/demo/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
```

Great.  Take a look and make sure that the keys have been generated correctly:
```nohighlight
$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDb89Y9dyiRcSmkwvi9pcjvpBZTfsD3Vlj/eoQWf1EvF+x10y/DvHfMz8MJthMeCNGTqg/lsj4t1FgcfV6/SkuvTV0mBT0T9+owAtzd+s92QOCzrbH99HgQGlOCu4IUrMqREd7sYfK67QFFVOdITEn9hqH1ZN1XVh3f7UXOCL6s6g5J2HatmYWLVf2hm6ZRUH1hcZnjlN+oui67HTarqFy/3efWVLdG+lc9gaSy9shzx4fUJZnVrzQw+KEVtgxdZGZgKaDfoMfmjFwHZNl+LIFqIMDy/CfJnmCE/oAVjTymcdKsOICIqbXqopG/eOufqed4wR2nxE+a6K2jWQVYUwFN root@ubuntu-s-1vcpu-1gb-nyc2-01
```

Slack the output of `cat ~/.ssh/id_rsa.pub` to the instructor so that they can add you to the remote machine.  Be sure that you are sending the `.pub` file and not your private key!

Once the instructor receives your key, tell the instructor to add  your key to the `~/.ssh/authorized_keys` directory; at which point you should be able to log in using the command:
```
$ ssh {your name}@{ip address}
```

Once you are on the machine, take a look around:
```
$ pwd  
/home/your-name
```

## App SpinUp

Your next step is going to be launching a simple app server on the remote machine.

Check out the [Simple Map App](https://gitlab.com/LaunchCodeTraining/simple-map-app) from GitLab.

First, you will have to build an executable jar file that you can transfer to the remote machine.  Typically you would also need to set up a web server as well, but in this case Spring Boot packages the server inside the jar file itself.

Open the command line in your Airwaze project and run the following command:
```nohighlight
$ gradle clean bootRepackage
```
<aside class="aside-note" markdown="1">
  Your project will need to have all tests passing in order for Gradle to successfully build an executable jar for your project.
</aside>

You should now have a `app-0.0.1-SNAPSHOT.jar` file located in the `ls build/libs/` directory of your project.

Remember that this jar file is actually just a zip file with a fancy name.  Use the following command to unzip the jar and see what is inside:
```nohighlight
cd build/libs
unzip build/libs/app-0.0.1-SNAPSHOT.jar
Archive:  build/libs/app-0.0.1-SNAPSHOT.jar
   creating: META-INF/
  inflating: META-INF/MANIFEST.MF    
   creating: BOOT-INF/
   creating: BOOT-INF/classes/
   creating: BOOT-INF/classes/com/
   creating: BOOT-INF/classes/com/launchcode/
   creating: BOOT-INF/classes/com/launchcode/gisdevops/
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/Airport.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/AirportController.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/AirportRepository.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/Application.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/Feature.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/FeatureCollection.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/FeatureSerializer.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/GeoJSONSerializer.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/Route.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/RouteController.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/RouteRepository.class  
  inflating: BOOT-INF/classes/com/launchcode/gisdevops/WktHelper.class  
  inflating: BOOT-INF/classes/application.properties  
  inflating: BOOT-INF/classes/flyway.conf  
  inflating: BOOT-INF/classes/geojson_parse.js  
  inflating: BOOT-INF/classes/import.sql  
   creating: BOOT-INF/classes/scripts/
   creating: BOOT-INF/classes/scripts/geojson_parse/
  inflating: BOOT-INF/classes/scripts/geojson_parse/.geojson_parse.js.swp  
  inflating: BOOT-INF/classes/scripts/geojson_parse/example.geojson  
  inflating: BOOT-INF/classes/scripts/geojson_parse/geojson_parse.js  
  inflating: BOOT-INF/classes/scripts/geojson_parse/package-lock.json  
  inflating: BOOT-INF/classes/scripts/geojson_parse/package.json  
   creating: BOOT-INF/classes/scripts/geojson_parse/test/
  inflating: BOOT-INF/classes/scripts/geojson_parse/test/.geojson_parse.spec.js.swp  
  inflating: BOOT-INF/classes/scripts/geojson_parse/test/geojson_parse.spec.js  
  inflating: BOOT-INF/classes/scripts/geojson_parse/test/invalid.geojson  
   creating: BOOT-INF/classes/static/
   creating: BOOT-INF/classes/static/css/
  inflating: BOOT-INF/classes/static/css/styles.css  
   creating: BOOT-INF/classes/static/elasticsearch-js/
  inflating: BOOT-INF/classes/static/elasticsearch-js/elasticsearch.angular.js  
  inflating: BOOT-INF/classes/static/elasticsearch-js/elasticsearch.angular.min.js  
  inflating: BOOT-INF/classes/static/elasticsearch-js/elasticsearch.jquery.js  
  inflating: BOOT-INF/classes/static/elasticsearch-js/elasticsearch.jquery.min.js  
  inflating: BOOT-INF/classes/static/elasticsearch-js/elasticsearch.js  
  inflating: BOOT-INF/classes/static/elasticsearch-js/elasticsearch.min.js  
  inflating: BOOT-INF/classes/static/index.html  
   creating: BOOT-INF/classes/static/js/
  inflating: BOOT-INF/classes/static/js/ol-debug.js  
  inflating: BOOT-INF/classes/static/js/script.js  
   creating: BOOT-INF/classes/static/json/
  inflating: BOOT-INF/classes/static/json/german_airports.geojson  
  inflating: BOOT-INF/classes/static/json/mexican_airports.geojson  
  inflating: BOOT-INF/classes/static/json/routes-sample.geojson  
   creating: BOOT-INF/lib/
 extracting: BOOT-INF/lib/spring-boot-starter-data-jpa-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-boot-starter-thymeleaf-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-boot-starter-web-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/hibernate-spatial-5.1.0.Final.jar  
 extracting: BOOT-INF/lib/postgresql-42.1.4.jar  
 extracting: BOOT-INF/lib/jackson-datatype-jts-2.4.jar  
 extracting: BOOT-INF/lib/spring-boot-starter-aop-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-boot-starter-jdbc-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-boot-starter-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/hibernate-entitymanager-5.0.12.Final.jar  
 extracting: BOOT-INF/lib/hibernate-core-5.0.12.Final.jar  
 extracting: BOOT-INF/lib/javax.transaction-api-1.2.jar  
 extracting: BOOT-INF/lib/spring-data-jpa-1.11.1.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-aspects-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/thymeleaf-spring4-2.1.5.RELEASE.jar  
 extracting: BOOT-INF/lib/thymeleaf-layout-dialect-1.4.0.jar  
 extracting: BOOT-INF/lib/spring-boot-starter-tomcat-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/hibernate-validator-5.3.4.Final.jar  
 extracting: BOOT-INF/lib/jackson-databind-2.8.7.jar  
 extracting: BOOT-INF/lib/spring-webmvc-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-web-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/hibernate-commons-annotations-5.0.1.Final.jar  
 extracting: BOOT-INF/lib/jboss-logging-3.3.0.Final.jar  
 extracting: BOOT-INF/lib/geolatte-geom-1.0.1.jar  
 extracting: BOOT-INF/lib/dom4j-1.6.1.jar  
 extracting: BOOT-INF/lib/jts-1.13.jar  
 extracting: BOOT-INF/lib/spring-boot-autoconfigure-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-boot-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-boot-starter-logging-1.5.2.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-context-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-aop-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-orm-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-jdbc-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-data-commons-1.13.1.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-tx-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-beans-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-expression-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/spring-core-4.3.7.RELEASE.jar  
 extracting: BOOT-INF/lib/snakeyaml-1.17.jar  
 extracting: BOOT-INF/lib/aspectjweaver-1.8.9.jar  
 extracting: BOOT-INF/lib/tomcat-jdbc-8.5.11.jar  
 extracting: BOOT-INF/lib/hibernate-jpa-2.1-api-1.0.0.Final.jar  
 extracting: BOOT-INF/lib/thymeleaf-2.1.5.RELEASE.jar  
 extracting: BOOT-INF/lib/javassist-3.21.0-GA.jar  
 extracting: BOOT-INF/lib/antlr-2.7.7.jar  
 extracting: BOOT-INF/lib/jandex-2.0.0.Final.jar  
 extracting: BOOT-INF/lib/jcl-over-slf4j-1.7.24.jar  
 extracting: BOOT-INF/lib/logback-classic-1.1.11.jar  
 extracting: BOOT-INF/lib/jul-to-slf4j-1.7.24.jar  
 extracting: BOOT-INF/lib/log4j-over-slf4j-1.7.24.jar  
 extracting: BOOT-INF/lib/slf4j-api-1.7.24.jar  
 extracting: BOOT-INF/lib/groovy-2.4.9.jar  
 extracting: BOOT-INF/lib/tomcat-embed-websocket-8.5.11.jar  
 extracting: BOOT-INF/lib/tomcat-embed-core-8.5.11.jar  
 extracting: BOOT-INF/lib/tomcat-embed-el-8.5.11.jar  
 extracting: BOOT-INF/lib/validation-api-1.1.0.Final.jar  
 extracting: BOOT-INF/lib/classmate-1.3.3.jar  
 extracting: BOOT-INF/lib/jackson-annotations-2.8.0.jar  
 extracting: BOOT-INF/lib/jackson-core-2.8.7.jar  
 extracting: BOOT-INF/lib/apfloat-1.8.2.jar  
 extracting: BOOT-INF/lib/xml-apis-1.4.01.jar  
 extracting: BOOT-INF/lib/tomcat-juli-8.5.11.jar  
 extracting: BOOT-INF/lib/ognl-3.0.8.jar  
 extracting: BOOT-INF/lib/unbescape-1.1.0.RELEASE.jar  
 extracting: BOOT-INF/lib/logback-core-1.1.11.jar  
   creating: org/
   creating: org/springframework/
   creating: org/springframework/boot/
   creating: org/springframework/boot/loader/
  inflating: org/springframework/boot/loader/LaunchedURLClassLoader$1.class  
  inflating: org/springframework/boot/loader/PropertiesLauncher$ArchiveEntryFilter.class  
  inflating: org/springframework/boot/loader/PropertiesLauncher$PrefixMatchingArchiveFilter.class  
  inflating: org/springframework/boot/loader/Launcher.class  
  inflating: org/springframework/boot/loader/ExecutableArchiveLauncher$1.class  
   creating: org/springframework/boot/loader/jar/
  inflating: org/springframework/boot/loader/jar/JarFile$1.class  
  inflating: org/springframework/boot/loader/jar/Handler.class  
  inflating: org/springframework/boot/loader/jar/JarEntry.class  
  inflating: org/springframework/boot/loader/jar/JarFile$3.class  
  inflating: org/springframework/boot/loader/jar/CentralDirectoryEndRecord.class  
  inflating: org/springframework/boot/loader/jar/CentralDirectoryVisitor.class  
  inflating: org/springframework/boot/loader/jar/JarFile$JarFileType.class  
  inflating: org/springframework/boot/loader/jar/JarFileEntries.class  
  inflating: org/springframework/boot/loader/jar/JarFile.class  
  inflating: org/springframework/boot/loader/jar/JarFileEntries$1.class  
  inflating: org/springframework/boot/loader/jar/JarURLConnection$1.class  
  inflating: org/springframework/boot/loader/jar/JarFile$2.class  
  inflating: org/springframework/boot/loader/jar/JarEntryFilter.class  
  inflating: org/springframework/boot/loader/jar/AsciiBytes.class  
  inflating: org/springframework/boot/loader/jar/CentralDirectoryParser.class  
  inflating: org/springframework/boot/loader/jar/Bytes.class  
  inflating: org/springframework/boot/loader/jar/ZipInflaterInputStream.class  
  inflating: org/springframework/boot/loader/jar/JarFileEntries$EntryIterator.class  
  inflating: org/springframework/boot/loader/jar/FileHeader.class  
  inflating: org/springframework/boot/loader/jar/JarURLConnection$JarEntryName.class  
  inflating: org/springframework/boot/loader/jar/JarURLConnection.class  
  inflating: org/springframework/boot/loader/jar/CentralDirectoryFileHeader.class  
   creating: org/springframework/boot/loader/data/
  inflating: org/springframework/boot/loader/data/ByteArrayRandomAccessData.class  
  inflating: org/springframework/boot/loader/data/RandomAccessDataFile$DataInputStream.class  
  inflating: org/springframework/boot/loader/data/RandomAccessDataFile$FilePool.class  
  inflating: org/springframework/boot/loader/data/RandomAccessData$ResourceAccess.class  
  inflating: org/springframework/boot/loader/data/RandomAccessDataFile.class  
  inflating: org/springframework/boot/loader/data/RandomAccessData.class  
  inflating: org/springframework/boot/loader/LaunchedURLClassLoader.class  
  inflating: org/springframework/boot/loader/JarLauncher.class  
  inflating: org/springframework/boot/loader/MainMethodRunner.class  
  inflating: org/springframework/boot/loader/PropertiesLauncher$1.class  
  inflating: org/springframework/boot/loader/ExecutableArchiveLauncher.class  
  inflating: org/springframework/boot/loader/WarLauncher.class  
   creating: org/springframework/boot/loader/archive/
  inflating: org/springframework/boot/loader/archive/JarFileArchive$EntryIterator.class  
  inflating: org/springframework/boot/loader/archive/ExplodedArchive$FileEntryIterator.class  
  inflating: org/springframework/boot/loader/archive/ExplodedArchive$FileEntry.class  
  inflating: org/springframework/boot/loader/archive/JarFileArchive$JarFileEntry.class  
  inflating: org/springframework/boot/loader/archive/Archive$Entry.class  
  inflating: org/springframework/boot/loader/archive/JarFileArchive.class  
  inflating: org/springframework/boot/loader/archive/ExplodedArchive.class  
  inflating: org/springframework/boot/loader/archive/Archive.class  
  inflating: org/springframework/boot/loader/archive/ExplodedArchive$FileEntryIterator$EntryComparator.class  
  inflating: org/springframework/boot/loader/archive/Archive$EntryFilter.class  
  inflating: org/springframework/boot/loader/archive/ExplodedArchive$1.class  
  inflating: org/springframework/boot/loader/PropertiesLauncher.class  
   creating: org/springframework/boot/loader/util/
  inflating: org/springframework/boot/loader/util/SystemPropertyUtils.class  
```

You'll notice that all the file contains three things:
1. The compiled class files of your project.
2. All of the libraries that your project requires (jar files).
3. The Spring Boot class files required to launch the server.

Make sure that your jar file is working properly on your local machine by running:
``` nohighlight
$ java -jar your-app.jar
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.2.RELEASE)

2018-02-08 06:34:31.676  INFO 15715 --- [           main] com.launchcode.gisdevops.Application     : Starting Application on Mikes-MBP with PID 15715 (/Users/mikemenne/Code/LaunchCode/Examples/GisDevops/airwaze-studio/build/libs/app-0.0.1-SNAPSHOT.jar started by mikemenne in /Users/mikemenne/Code/LaunchCode/Examples/GisDevops/airwaze-studio/build/libs)
2018-02-08 06:34:31.683  INFO 15715 --- [           main] com.launchcode.gisdevops.Application     : No active profile set, falling back to default profiles: default
2018-02-08 06:34:32.493  INFO 15715 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@2cdf8d8a: startup date [Thu Feb 08 06:34:32 CST 2018]; root of context hierarchy
2018-02-08 06:34:34.934  INFO 15715 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'org.springframework.transaction.annotation.ProxyTransactionManagementConfig
```

Next transfer your project's executable jar file to the remote server.  To transfer the file, you'll use a command called `scp`.  The command looks somthing like this:
```nohighlight
$ # scp {file you would like to transfer}  {username}@{host}:{directory on the remote machine}
$ scp your-app.jar trainee@{ip address of machine}:/home/trainee
```

If you SSH into your remote machine, you should see the file has been transferred to your home directory.
```nohighlight
$ ssh trainee@{ip address of machine}
$ ls 
your-app.jar
```

In this exercise, we will just run the file locally in your home directory. 
```
$ java -jar your-app.jar  &
```

This might work, but since everyone is trying to run their app on the same port, we should probably customize the app so that they can run on different ports.

Open the `my-map-app` and add the following line to the application.properties file.  
```nohighlight
server.port=${APP_PORT}
```

Run the app and you should see that the app server has started up on port 9001.

<aside class="aside-note" markdown="1">
 Anytime you exit your bash, the environment variable that you set will disappear.  If you would like to make your environment variable persistent, add the line to your `~/.bash_rc` file.  The `~/.bash_rc` file is run every time the terminal is accessed from your user. 
</aside>

Once everyone has their servers up and running, use the following command to monitor server performance:
```
$ htop
```

Before we end the walkthrough, let's go ahead and kill the server we just started.  Note that since the process is running as a daemon (in the background), ctrl-c will not kill it.  

Instead run the following commands:
```nohighlight
$ ps aux | grep my-app
```

Find the process id and send the kill signal to the process:
```nohighlight
$ kill -9 {process-id}
```

The end.  The Studio will be coming up soon.
