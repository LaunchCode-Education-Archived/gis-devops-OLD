---
title: "Studio: Springfox and Swagger"
---

In this studio, we will be learning how to use Springfox to autogenerate the Swagger docs.

## Getting Ready
Create a `add-springfox` branch off of your `launchcart\rest-studio-solution`. Do NOT create the branch off of the walkthrough studio.

## Configure Springfox
Add these to your `dependencies {` in `build.gradle`
```no-highlight
	compile(group: 'io.springfox', name: 'springfox-swagger2', version: '2.7.0')
	compile(group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.7.0')
```

Add `@EnableSwagger2` annotation to `LaunchcartApplication`
```java
  @EnableSwagger2
  @SpringBootApplication
  public class LaunchcartApplication {

    public static void main(String[] args) {
      SpringApplication.run(LaunchcartApplication.class, args);
    }
  }
```

Now go to [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html).

## Tasks
- Figure out how to change `AuthenticationInterceptor.preHandle` to allow this request to work when you are NOT logged in [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)
- Only show `/api` endpoints in the Swagger report

## Bonus Mission
- Add Swagger documentation to your Airwaze app

## Turn in your Work
* Commit and push your work to GitLab.
* Create a MergeRequest and post it to slack
* Notify the instructor that you are done, along with the name of the branch that you completed your work in.
