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

In the `test` module, create the following class:

```java
public abstract class AbstractBaseRestIntegrationTest {

    protected MediaType contentType = new MediaType(MediaType.APPLICATION_JSON.getType(),
            MediaType.APPLICATION_JSON.getSubtype(),
            Charset.forName("utf8"));

    private HttpMessageConverter mappingJackson2HttpMessageConverter;

    @Autowired
    void setConverters(HttpMessageConverter<?>[] converters) {

        this.mappingJackson2HttpMessageConverter = Arrays.asList(converters).stream()
                .filter(hmc -> hmc instanceof MappingJackson2HttpMessageConverter)
                .findAny()
                .orElse(null);

        assertNotNull("the JSON message converter must not be null",
                this.mappingJackson2HttpMessageConverter);
    }

    protected String json(Object o) throws IOException {
        MockHttpOutputMessage mockHttpOutputMessage = new MockHttpOutputMessage();
        this.mappingJackson2HttpMessageConverter.write(
                o, MediaType.APPLICATION_JSON, mockHttpOutputMessage);
        return mockHttpOutputMessage.getBodyAsString();
    }

}
```

This class contains a couple of utility methods to handle serializing Java objects for the purposes of testing. In the `test` module, create a class `ItemRestControllerTests` that extends `AbstractBaseRestIntegrationTest`.

We will now implement the following resources:

- `GET /api/items` (with parameter `price`)
- `GET /api/items/{id}`
- `POST /api/items`
- `PUT /api/items/{id}`
- `DELETE /api/items/{id}`