---
title: "Walkthrough: Spring Integration Tests"
---

## Setup

1. Fork this repo TODO
2. Open project in Intellij by opening gradle.build file as a project

## Spring Integration Test Utilities
###Class-level Configuration
Annotation | What it does
|---------|--------| 
 `@RunWith(SpringRunner.class)` | runs tests with the given test runner
 `@SpringBootTest(classes | Application.class)` |ensures proper Spring web app context is loaded (including loading of framework components like controllers, DAOs, etc)
 `@TestPropertySource(locations = "classpath:application-test.properties")` | replaces use of application.properties with the given file
 `@AutoConfigureMockMvc` | allows for autowiring of MockMvc instance
 `@Transactional` | wraps each test method in a transaction, and rolls it back after each method runs

###Configure Test Database Connection
- Use a separate properties file (w/ @TestPropertySource) to configure a separate data source
  - Create file `PORJECT-ROOT/src/test/resources/application-test.properties`
  ```
  spring.datasource.driver-class-name=org.h2.Driver
  spring.datasource.url=jdbc:h2:mem:test
  spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.H2Dialect
  spring.jpa.hibernate.ddl-auto=update
  ```
- Add h2 dependency to build.gradle
```
dependencies {
	//Add this entry to include the h2 in memory database
	compile('com.h2database:h2')

  //other libaries will be listed already, such as
  compile('org.springframework.boot:spring-boot-starter-data-jpa')
  compile('org.springframework.boot:spring-boot-starter-thymeleaf')
  compile('org.springframework.boot:spring-boot-starter-web')
  runtime('org.springframework.boot:spring-boot-devtools')
}
```
###Executing and Verifying Results
Method | Info
|----|-----|
`MockMvc.perform(RequestBuilder requestBuilder)` | Perform a request and return a type that allows chaining further actions, such as asserting expectations, on the result. [More info](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html)
`MockMvcRequestBuilders.get(String uri)` | static method that performs a GET request. Returns a MockMvcRequestBuilder that can be chained. [More info](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/request/MockMvcRequestBuilders.html)
`MockMvcRequestBuilders.post()` | static method that performs a GET request. Returns a MockMvcRequestBuilder that can be chained.  [More info](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/result/MockMvcResultMatchers.html)
`MockMvcRequestMatchers - content(), jsonPath(), status()` | The type of result to expect: HTML, JSON, status...  [More info]()
`ResultActions - andExpect()` | What to look for in the result: status code, a string, JSON value...  [More info](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultActions.html)

Example
```
  //make a GET request to /cars/99
  mockMvc.perform(get("/cars/99"))
      //expect the HTTP status to be 200 ok
      .andExpect(status().isOk()) 
      //the HTML returned should contain text "Tesla"
      .andExpect(content(string(containsString("Tesla"))));
```

## Resources
 - http://www.h2database.com/html/main.html
 - https://docs.spring.io/spring/docs/current/spring-framework-reference/testing.html#integration-testing
