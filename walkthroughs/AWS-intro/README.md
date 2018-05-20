---
title: "Walkthrough: Intro to AWS"
---

We will walkthrough getting signed into AWS console and then have a look around

## Look Around AWS Console

### Top Menu
* Has drop down menu for your account
* Has drop down menu for the current Region
* Support Menu for getting help or finding docs

### Regions
Regions are seperate geographic locations where you can deploy AWS services. Everything in the AWS UI is based off of 
Region, so if you don't see a instance you created you are probably in the wrong Region. [More about Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)

![AWS Regions](../../materials/week05/regions.png)

### Services Search Bar and Menu
This is where AWS can get a little overwhelming because there are so many services available. We are only to look at a 
few in the class.  On your own time, please read about all the new terms/tools you see listed here. You may want to use 
them some day. Our focus today is `EC2`.

![AWS Services](../../materials/week05/services.png)

## Setup You AWS Account

### Logging into AWS

* Please check your email for an invite to AWS Console from your instructor
* Reset Password

### Add Two Factor Authentication
1. Go to **IAM** via **Services**
2. Click **Users**
3. Click your username
4. Click **Security credentials** tab
5. Click the pencil for **Assigned MFA device**
6. Download an Authenticator such as Google Authenticator on your phone
7. Open the Authenticator app
8. Now connection your Authenticator app by entering informatin provided by AWS
9. Now you have to enter a key from your Auth app into AWS and then wait for it to expire and enter the next key that appears
10. Now log out and see if works. Fingers crossed.
