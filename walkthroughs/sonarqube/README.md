---
title: "Walkthrough: Sonarqube"
---

Follow along with the instructor as we configure Sonarqube.

## Setup
Create a new feature branch in your `airwaze-studio` repoo. Name it `add-sonarqube`.  When finished with this walkthrough you can merge it into your `master` branch.

## Continuous Inspection
Sonarqube is an open source tool for continuous inspection of code quality including: bug detection, code smells, and security vulnerabilities.

We will use Sonarqube to analyze our Java code. It will search for duplicate code, unused variables/parameters, code smells, and much more. Fixing the issues found by sonarqube helps to 
keep our code consistent between developers and hopefully more secure and stable. Consider sonarqube to be Eslint for Java, with more features. Note that sonarqube will analyze multiple language, however for this class we are only using it for Java.

Sonarqube runs as a service. Project configuration and reports are viewable in your browser via web interface. Defaults to `http:localhost:9000` if installed locally.

Sonarqube can integrate with other tools such as gradle and jenkins.

## Install and Configure Sonarqube
- Go to [https://www.sonarqube.org](https://www.sonarqube.org)
- Click **Download**
- Choose the **LTS Release**
- Unzip the downloaded file to `~/sonarqube-6.7.1`. (Note your version may vary)
- Open a terminal and run this command `$ ~/sonarqube-6.7.1/bin/macosx-universal-64/sonar.sh start`
- Now go to this address in your browser: `http:localhost:9000`
- Login with default user. username: admin password: admin
- Enter `airwaze` as the project key

## Configure your Java Project
- Add both of these to your `build.gradle` file
```javascript
plugins {
    id "org.sonarqube" version "2.5"
}
```
```javascript
sonarqube {
    properties {
      property "sonar.projectKey", "airwaze-studio"
    }
}
```

## Run Sonarqube Gradle Task
- In terminal or via the Gradle menu in Intellij, run the `sonarqube` gradle task (note it's under *other* in intellij gradle menu)
- This gradle task runs other gradle tasks like `compileJava` and then creates a report that is viewable on the sonarqube web app


## View the Results in Sonarqube
- Navigate to `http://localhost:9000/projects`
- Click on the project name
- On the project page, click on the numbers in each category to get a detailed report about that topic
