---
title: Elasticsearch Queries Studio 2
---

## Kibana Setup

Install Kibana by running the following HomeBrew command:

```nohighlight
$ brew install kibana
```

To start the Kibana server in the background, run the following command:

```nohighlight
$ kibana &
```

Navigate to [http://localhost:5601](localhost:5601) to view the running server.

Take a second to look at each of the pages below. They may be beneficial during your development:

1. [Dev Tools](http://localhost:5601/app/kibana#/dev_tools) - Use the Dev Tools to run queries against your ElasticSearch (without cURL!)
2. [Discover](http://localhost:5601/app/kibana#/discover) - lists all of the data in your ElasticSearch instance.
3. [Visualize](http://localhost:5601/app/kibana#/visualize) - Perform analysis and create graphics describing your data.
4. [Dashboard](http://localhost:5601/app/kibana#/dashboard) - Combine graphs to give you a comprehensive picture of your data.

<aside class="aside-note" markdown="1">
If your indices are listed in the Discover pane, you're likely missing a default index pattern. Add the `twitter` index as a default pattern. This can be changed later in the Management pane.
</aside>

## Using Kibana

Open the [Dev Tools](http://localhost:5601/app/kibana#/dev_tools). Let's run a few queries.

Instead of using cURL. Queries can be put into Dev Tools by specifing the HTTP verb and the query text.

```json
GET _search
{
  "query": {
    "match_all": {
      ""
    }
  }
}
```

Also, notice that the left panel provides error messages with the line number of the error. In this case we have some errand quote marks in our query. Remove them and run the query again.

Next, open the [Discover Page](http://localhost:5601/app/kibana#/discover). This page provides a paginated list of all of the documents in your cluster. The search bar provides a quick way to search by attribute name.

Next, click on the [Visualize Page](http://localhost:5601/app/kibana#/visualize). This page allows you to create detailed graphs of your data.

Click on create a visualization and create a simple visualization of how many books are in the ElasticSearch.

## Loading Data

First, let's delete the data in the `twitter` index:

```
$ curl -XDELETE 'localhost:9200/twitter'
```

As we did yesterday, let's load some data. Create a script:

```
$ touch load_data_geo.sh
$ chmod +x load_data_geo.sh
```

Then copy/paste the contents of [this gist](https://gist.github.com/chrisbay/8ef471ed1ac903c2bcaa2b82b49917a4). Save and run the script:

```
$ ./load_data_geo.sh
```

Verify that the mapping was created properly by running the following command and checking that the `location` field is of type `geo_point`:

```
$ curl 'localhost:9200/twitter/_mappings?pretty'
```

If something went wrong, delete the index, get help from an instructor, and try again.

## Your Tasks

Now you'll get some practice with Elasticsearch. Carry out each of the following tasks. Once you have a successful query/command for each, save the commend in a `.txt` file for submission

1. Find all tweets with between 5 and 10 likes, inclusive of those endpoints.
1. Find all tweets by Mary Jones that have at least 2 likes
1. Find all tweets that contain the text "Elasticsearch", including mispellings up to distance of 3 away.
1. Find all tweets that have a location (Hint: Try the [exists query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-exists-query.html))
1. Find all tweets that do not have a location
1. Find the average number of likes for tweets that have a location
1. Find the average number of likes for tweets that do not have a location
1. Find all tweets with locations within 500km of Boise, ID

## Turning In Your Work

Submit your queries to an instructor via Slack once you're finished.