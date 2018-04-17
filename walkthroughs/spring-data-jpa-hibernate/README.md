---
title: "Walkthrough: Cars Demo - Spring Data, JPA, Hibernate"
---

In this walkthrough, we revisit the [Car Integration Tests](https://gitlab.com/LaunchCodeTraining/car-integration-test-starter) that we worked on in [yesterday's walkthrough](../walkthroughs/spring-integration-tests/). We'll refactor the app to use Postgres and Hiberante.

## Setup

1. In the terminal go to project folder for *Car Integration Tests*
2. Checkout the branch that contains the solution for the Day 3 walkthrough. Example: `$ git checkout master` or `$git checkout solution`
3. Create a new branch for today's work: `$ get checkout -b day4-add-hibernate`

## Our Tasks

Each section outlines one task or group of tasks we need to complete.

### Set Up A Postgres DB

1. From `psql`, create a Postgres user: `psql=# create user cart_user with encrypted password 'catdogbluehouse';`
2. Create a database: `psql=# create database cart;`
3. Grant the user access to the database:`psql=# grant all privileges on database cart to cart_user`


### Configure the App To Use the Database

In intellij, open `/car-integration-tests/src/main/resources/application.properties` then set each of these values:

```nohighlight
spring.datasource.driver-class-name=
spring.datasource.url=jdbc:postgresql://HOST-HERE:5432/DB-NAME-HERE
spring.datasource.username=USERNAME-HERE
spring.datasource.password=PASSWORD-HERE
spring.jpa.hibernate.ddl-auto=
```

<aside class="aside-warning" markdown="1">
  Committing passwords to source control is a BAD idea, ESPECIALLY a public repo!
</aside>

Then add the associated Postgres dependency to `build.gradle` as a compile-time dependency. 
Set h2 dependency to be a `testCompile` dependency, since it will only be needed for running tests at this point.
```java
dependencies {
	runtime('org.springframework.boot:spring-boot-devtools')
	compile('org.springframework.boot:spring-boot-starter-data-jpa')
	compile('org.springframework.boot:spring-boot-starter-thymeleaf')
	compile('org.springframework.boot:spring-boot-starter-web')
	compile(group: 'org.postgresql', name: 'postgresql', version: '42.1.4')
	testCompile('com.h2database:h2')
	testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

### Add Annotations to Car Model
```java
package org.launchcode.training.models;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Car {

    @Id
    @GeneratedValue(strategy= GenerationType.IDENTITY)
    private int id;
    //rest of class is not shown...
```

### Add CarRepository
```java
package org.launchcode.training.data;

import org.launchcode.training.models.Car;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface CarRepository extends JpaRepository<Cart, Integer> {
}
```

### Autowire Repositories into Controllers
```java
@Controller
@RequestMapping("car")
public class CarController {

    @Autowired
    private org.launchcode.training.data.CarRepository carRepository;
```

### Autowire Repositories in Tests
```java
@RunWith(SpringRunner.class)
@IntegrationTestConfig
public class CarControllerTests {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private CarRepository carRepository;
```

### Configure Test DB
Add a `/car-integration-tests/src/test/resources/application-test.properties` file with below contents.
```java
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.url=jdbc:h2:mem:test
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=update
```

We need to make sure our test properaties are used when running tests. Add the below code to `/car-integration-tests/src/test/java/org/launchcode/launchcart/IntegrationTestConfig.java`
```java
@TestPropertySource(locations = "classpath:application-test.properties")
```

### Do the Tests Pass?
If not, fix them ;p