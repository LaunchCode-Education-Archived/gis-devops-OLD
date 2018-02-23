---
title: "Walkthrough: Jenkins"
---

Follow along with the instructor as we configure Jenkins.

## Our Continous Integration Goals
After a branch/story is merged into our `master` branch. We want...
1. Make sure the code compiles
2. Makes sure the tests pass
3. Deliver a `.jar` file that is ready to be deployed

## Install and Configure Jenkins
- Download jenkins from [jenkins home page](https://jenkins.io/download/) by clicking *Generic Java War* options at the bottom
- Copy the `jenkins.war` file the folder to `~/jenkins`
- Now start jenkins
```
$ java -jar ~/jenkins/jenkins.war --httpPort=9090
```
<aside class="aside-note" markdown="1">
Normally you would not install jenkins on your dev machine. You would isntall it on a server that would be continsouly running so that it could react to a commit at anytime.
</aside>

#### Configure Jenkins
- Go to `http:localhost:9090`
- You should see screen asking for an admin key
- In terminal view the file listed and copy the key inside it
- Paste that key into the input box

#### Install Plugins
- Click install suggested plugins (this will take a couple minutes)

#### Create a Jenkins User
- This will be how you login to jenkins going forward
- Be sure to remember the username and password

## Create Project to Compile Airwaze
- Click *New Item*
- Enter name `Airwaze Compile`
- Click *Freestyle Project*
- Click *Ok* at bottom

#### Configure the Compile Project
- In *Source Code Management* click *Git*
- Post your gitlab repo url into *Repository URL*
- Make sure you the branch you want to compile is in the *Branch Specifier* field 
- Go to the *Build Triggers* section
- Select *Poll SCM* and enter `H/5 * * * *` into the *Schedule* input 
- Go to the *Build* section
- Click *Add build step*
- Click *Invode Gradle script*
- Enter `clean compileJava` into the *Tasks* input

Now test out the build by clicking *Build Now*

#### We Need to Install a Plugin
- Go to your jenkins home page `http://localhost:9090`
- Click *Manage Jenkins* on the left
- Click *Manage Plugins* on the right
- Click *Available*
- Enter *Parameterized Trigger* in search box
- Install *Parameterized Trigger plugin* without restarting

## Create Test, CreateJar, and DeployToS3 Projects
- Create three more *Freestyle* projects
- `Airwaze Test`
- `Airwaze CreateJar` 
- `Airwaze Deliver`
- Don't do anything but give these a name. We will configure them next.

### Edit the Compile Project
We need the *Compile Project* to kick off the *Test Project* when it's done. We also want the two projects to share the same work space, so that the repo doesn't have to be checked out again.
- Navigate to project `http://localhost:9090/job/Airwaze%20Compile/`
- Click *Configure*
- Go to *Post Build Actions*
- Enter `Airwaze Test` as the project to build
- Click *Add Parameters* and select *Build on the same node*
- Click *Add Parameters* again and select *Predefined parameters*
- Enter this `AIRWAZE_WORKSPACE=${WORKSPACE}` into input
- Click save

#### Configure Test Project
- Navigate to project `http://localhost:9090/job/Airwaze%20Test/`
- In *General* select *This project is parameterized*
- Paste this `AIRWAZE_WORKSPACE` into *name* input
- Click *Advanced* button and select *Custom Workspace*
- Enter `${AIRWAZE_WORKSPACE}` in the input
- Go to the *Build* section
- Click *Add build step*
- Click *Invode Gradle script*
- Enter `clean test` into the *Tasks* input
Now we need to kick off the *CreateJar Project*
- Go to *Post Build Actions*
- Enter `Airwaze CreateJar` as the project to build
- Click *Add Parameters* and select *Build on the same node*
- Click *Add Parameters* again and select *Predefined parameters*
- Enter this `AIRWAZE_WORKSPACE=${WORKSPACE}` into input
- Click save

Run the Test Project
- You can view the tests by finding the test results in the project `works space/builds/reports/tests/test`
- Or we can configure the tests results to be pushlised on the project results
- Go to the *Post build actions* for the *Test Project*
- Select *Publish JUnit test result report* and input this `build/test-results/test/*.xml` into input
- Run the project again and you will see the test results on the project page

#### Configure CreateJar Project
- Same configuration as the *Test Project*, with these exceptions
- In the *Build* section run this gradle command `bootRepackage`
- In *Post Build Actions* project to build enter `Airwaze Deliver`
- Do not have the test results copied as we did with the *Test Project*

#### Setup S3 Bucket (Needed so we can configure the next project)
 - If you haven't already, you need to install `awscli`. Instructions can be found in the [AWS3 Studio](https://education.launchcode.org/gis-devops/studios/AWS3/)
 - Create a new S3 bucket that will used for the `.jar` files your jenkins builds produce
 - Be sure to create the new bucket with **VERSIONING** enabled
 
 Make sure your s3 bucket shows up when you run this command in terminal
 ```
 $ aws s3 ls
 ```

#### Configure Deliver Project
- Same configuration as *CreateJar Project*, with these two exceptions
- In the *Build* section select *Execute shell*
- Enter this into input `aws s3 cp build/libs/app-0.0.1-SNAPSHOT.jar s3://YOUR-S3-BUCKET/`
- There are NO *Post Build Actions*

##That's It!
Now run the *Airwaze Compile* project now and watch it kick off the other projects automatically!
