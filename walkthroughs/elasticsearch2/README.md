---
title: Elasticsearch Walkthrough 2
---

## Kibana Setup

Install Kibana by running the following HomeBrew command:

```nohighlight
$ brew install kibana
```

To start the Kibana server in the background, run the following command:

```nohighlight
$ Kibana &
```

Navigate to [http://localhost:5601](localhost:5601) to view the running server.

Take a second to look at each of the pages below. They may be beneficial during your development:

1. [Dev Tools](http://localhost:5601/app/kibana#/dev_tools) - Use the Dev Tools to run queries against your ElasticSearch (without cURL!)
2. [Discover](http://localhost:5601/app/kibana#/discover) - lists all of the data in your ElasticSearch instance.
3. [Visualize](http://localhost:5601/app/kibana#/visualize) - Perform analysis and create graphics describing your data.
4. [Dashboard](http://localhost:5601/app/kibana#/dashboard) - Combine graphs to give you a comprehensive picture of your data.

<aside class="aside-note" markdown="1">
If you indices are listed in the Discover pane, you're likely missing a default index pattern. Add the `book` index as a default pattern. This can be changed later in the Management pane.
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

Also, notice that the left panel provides error messages with the line number of the error. In this case we have a comment in our query. Remove the comment and run the query again.

Next, open the [Discover Page](http://localhost:5601/app/kibana#/discover). This page provides a paginated list of all of the documents in your cluster. The search bar provides a quick way to search by attribute name.

Next, click on the [Visualize Page](http://localhost:5601/app/kibana#/visualize). This page allows you to create detailed graphs of your data.

Click on create a visualization and create a simple visualization of how many books are in the ElasticSearch.

## Document Mappings

Let’s get a little more in-depth with document mappings. Remember our practice server from yesterday? Let’s see how that data was mapped, make our own mapping, and upload some data.

Let's check the health of our cluster before diving in.

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/_cat/health?v&pretty'
```

If your server isn't running, start it up with:

```nohighlight
$ brew services start elasticsearch
```

<aside class="aside-note" markdown="1">
We'll use curl for accessing the `_cat` API, but you may use either curl or the Dev Tools console of Kibana for running the queries below.
</aside>

## Retrieve a Mapping

All being well, let’s take a peek at how the book documents are mapped by making a request to `/book/_mapping`.

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/book/_mapping?&pretty'
```

Your response should look very much like this:

```json
{
  "book" : {
    "mappings" : {
      "doc" : {
        "properties" : {
          "author_name" : {
            "type" : "text"
          },
          "book_id" : {
            "type" : "integer"
          },
          "description" : {
            "type" : "text"
          },
          "location" : {
            "type" : "geo_point"
          },
          "title" : {
            "type" : "text"
          },
          "year" : {
            "type" : "integer"
          }}}}}}
```

Let’s compare that to the mapping of the local book index we used in yesterday’s studio.

```nohighlight
$ curl -XGET 'localhost:9200/book/_mapping?&pretty'
```

Looks pretty different, huh?

At the time of this writing, in ES v6.1.1, fields mapped dynamically pretty much all default to keyword text fields. You might get something very different depending upon which version you are using.

By contrast, we set up the mapping on the Bonsai sample index deliberately. These different field types allow different types of searches to be run against them. You can also specify analyzers, but it will default to a pre-packaged English analyzer if none is specified.

This is the command we used to set up the mapping you see on the sample cluster:

```nohighlight
$ curl -XPUT ‘url_omitted_do_not_run_this/book?pretty' -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "doc": {
      "properties": {
        "title":    { "type": "text"  },
        "author_name":     { "type": "text"  },
        "year":      { "type": "integer" },
        "description":     { "type": "text"  },
        "book_id":      { "type": "integer" },
        "location":  { "type":   "geo_point" }
      }}}}'
```

This adds mappings for additional fields, and specifies non-text types for a couple of fields as well.

## Create a Mapping

For the studio, we are going to want our airport data to be usable in Elasticsearch.

Let’s first take a look at our airport data and make a mapping for it. The data we have looks like this:

```nohighlight
1,"Goroka Airport","Goroka" ,"Papua New Guinea",GKA,AYGA,5282,Pacific/Port_Moresby,"Point(145.391998291 -6.081689834590001)"
2,"Madang Airport","Madang" ,"Papua New Guinea",MAG,AYMD,20,Pacific/Port_Moresby,"Point(145.789001465 -5.20707988739)"
```

Here’s a mapping we could start with for our airport data. Note that this one will create the index and map it in one step. You will not need to create it separately.

```nohighlight
$ curl -XPUT 'localhost:9200/airwaze?pretty' -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "doc": {
      "properties": {
        "airport_id":      { "type": "text" },
        "name":     { "type": "text"  },
        "ident":    { "type": "text"  },
        "elevation_ft":      { "type": "double" },
        "location":  { "type":   "geo_point" }
      }}}}'
```

Confirm that it worked:

```nohighlight
$ curl -XGET 'localhost:9200/_cat/indices?v&pretty'
$ curl -XGET 'localhost:9200/airwaze/_mapping?&pretty'
```

<aside class="aside-note" markdown="1">
Consult the [docs](https://www.elastic.co/guide/en/elasticsearch/reference/6.1/mapping.html) for more supported types.
</aside>

## Upload Some Data

Remember yesterday when we add books to our index?

```nohighlight
$ curl -XPOST 'localhost:9200/book/doc?pretty&pretty' -H 'Content-Type: application/json' -d '
  {
    "title": "Persuasion",
    "book_id”: “17”,
    “author_name”: “Jane Austen”
  }'
```

Let’s do something similar with an airport.

```nohighlight
$ curl -XPOST 'localhost:9200/airwaze/doc?pretty&pretty' -H 'Content-Type: application/json' -d'
  {
    "name": "Goroka Airport",
    "airport_id": 1,
    "ident": "GKA",
    "elevation_ft": 5282,
    "location" : { "lon": 145.391998291, "lat": -6.081689834590001}
  }'
```

And check for success:

```nohighlight
$ curl -XGET 'localhost:9200/airwaze/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "size": 10
}'
```

It might take some time to upload all large data sets, but usually that process is scripted and run automatically. Often a library is used for that.

Some Elasticsearch clients include `bulk_import`. Bulk imports are more efficient than single imports. Here’s some documentation about [bulk_import](https://www.elastic.co/guide/en/elasticsearch/reference/6.1/docs-bulk.html)
