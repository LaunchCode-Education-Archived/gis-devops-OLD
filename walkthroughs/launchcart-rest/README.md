---
title: "Walkthrough: LaunchCart REST"
---

In this walkthrough, the instructor will guide you through adding some RESTful endpoints to the LaunchCart application.

## Getting Started


From the same [`launchcart` project/repository](https://gitlab.com/LaunchCodeTraining/launchcart) that you used previously, fetch changes from the remote and check out the `rest-walkthrough` branch.

```nohighlight
$ git fetch
$ git checkout rest-walkthrough
```

This starter code has some functionality beyond what you added in the [LaunchCart 2 studio](../../studios/launchcart2/). In particular, it has a `Customer` class, along with functionality for users to register and log in as customers.

## Adding a REST Controller

Let's complete a few setup steps before starting to code:

- Create a new package, `org.launchcode.launchcart.controllers.rest`
- Create a new class in his package named `ItemRestController`
- Annotate the class with `@RestController`

In the `test` module, note that there are two new classes. `AbstractBaseRestIntegrationTest` class contains a couple of utility methods to handle serializing Java objects for the purposes of testing. And `ItemRestControllerTests` extends `AbstractBaseRestIntegrationTest` and contains the integration tests for the functionality that we'll be adding. We'll review this code in class.

We will now implement the following resources:

- `GET /api/items` (with parameter `price`)
- `GET /api/items/{id}`
- `POST /api/items`
- `PUT /api/items/{id}`
- `DELETE /api/items/{id}`