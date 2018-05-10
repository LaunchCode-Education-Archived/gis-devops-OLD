---
title: "Walkthrough: Elasticsearch in Spring"
---

We'll walk through the steps to integrate Elasticsearch with Spring, using the Launchcart application.

## Integrating Elasticsearch

We'll walk through several steps needed to use Elasticsearch within Spring.

<aside class="aside-note" markdown="1">

The code that we'll look at is in the `elasticsearch` branch of the `LaunchCodeTraining/launchcart` repository.

To view the specific changes, look at [this commit](https://gitlab.com/LaunchCodeTraining/launchcart/commit/cb706a682be3fb432fe4a3dd9c27963d76f30a5e).

</aside>

1. Add Gradle dependencies
    ```
    compile('org.springframework.boot:spring-boot-starter-data-elasticsearch:1.5.10.RELEASE')
	compile(group: 'org.elasticsearch.client', name: 'transport', version: '6.2.3')
    ```
1. Create the `EsConfig` class to setup an embedded Elasticsearch instance
1. Write an integration test for the desired behavior
1. Create the `ItemDocument` and `ItemDocumentRepository` classes
1. Create `ItemDocumentController` and implement the `search` method
1. Create `EsController` and `EsUtils` to enable admin-oriented interactions with the ES instance

<aside class="aside-warning" markdown="1">

This approach uses the `TransportClient` class to connect to a cluster over port 9300 via the transport protocol. This technique requires that the ES instance and the `TransportClient` have the _same major versions_. Elasticsearch is in the process of replacing this client with a REST API client that will be version agnostic.

Read more about the [state of the official Elasticsearch Java clients](https://www.elastic.co/blog/state-of-the-official-elasticsearch-java-clients).

</aside>

## Your Tasks

On your own, study the code above and make sure you understand each of the components, referring to the linked resources below as necessary. When you come across something that isn't clear, talk through it with another student or with an instrutor.

## Bonus Missions

We looked at how to push a new item to Elasticsearch when creating it via the REST API. There are still several tasks that can be immediately carried out to fully integrate ES with the application. Try one more more of the following:

* Push a new document to ES when adding an item via the web view.
* Update a document in ES when updating via the API or web view.
* Add a search view that displays results of a fuzzy search. This may be done either via an AJAX request to `ItemDocumentRepository.search`, or by creating a new controller method that passes fuzzy search results into a template.

## Resources 

- [Spring Data Elasticsearch tutoria](http://www.baeldung.com/spring-data-elasticsearch-tutorial)
- [ElasticSearchRepository](https://docs.spring.io/spring-data/elasticsearch/docs/current/api/org/springframework/data/elasticsearch/repository/ElasticsearchRepository.html)
- [TransportClient](https://www.elastic.co/guide/en/elasticsearch/client/java-api/6.2/transport-client.html)
- [QueryBuilders](https://static.javadoc.io/org.elasticsearch/elasticsearch/2.4.0/org/elasticsearch/index/query/QueryBuilders.html)
- [Spring Data Elasticsearch Queries](http://www.baeldung.com/spring-data-elasticsearch-queries)
- [The @Value annotation](http://www.baeldung.com/spring-value-annotation)