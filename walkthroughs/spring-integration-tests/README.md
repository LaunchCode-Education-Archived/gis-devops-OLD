---
title: "Walkthrough: Spring Integration Tests"
---

## Setup

1. Fork this repo: [Car Integration Tests](https://gitlab.com/LaunchCodeTraining/car-integration-test-starter)
2. Open project in Intellij by opening gradle.build file as a project

## What we will do
1. Add an integration test for going to the url `/car`
2. Add an integartion test for going to the url `/car/:id`
3. Implement the route /car/:id (make the tests green)

## Spring Integration Test Utilities
###Class-level Configuration
Annotation | What it does
|---------|--------| 
 `@RunWith(SpringRunner.class)` | runs tests with the given test runner
 `@SpringBootTest(classes | Application.class)` |ensures proper Spring web app context is loaded (including loading of framework components like controllers, DAOs, etc)
 `@TestPropertySource(locations = "classpath:application-test.properties")` | replaces use of application.properties with the given file
 `@AutoConfigureMockMvc` | allows for autowiring of MockMvc instance
 `@Transactional` | wraps each test method in a transaction, and rolls it back after each method runs

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
 - https://docs.spring.io/spring/docs/current/spring-framework-reference/testing.html#integration-testing
