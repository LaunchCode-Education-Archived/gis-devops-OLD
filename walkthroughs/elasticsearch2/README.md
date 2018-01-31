---
title: Walkthrough
---



Let’s get a little more in-depth with document mappings. Remember our practice server from yesterday? Let’s see how that data was mapped, make our own mapping, and upload some data.

First, as always, test the cluster health before running queries (at least we know it was up when we started).

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/_cat/health?v&pretty'
```

##Retrieve a Mapping
All being well, let’s take a peek at how the book documents are mapped:

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/book/_mapping?&pretty'
```

Your response should look very much like this:

```nohighlight
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

Looks pretty different huh?

At the time of this writing, in ES v 6.1.1, fields mapped dynamically pretty much all default to keyword text fields. You might get something very different depending upon which version you are using.

By contrast, we set up the mapping on the Bonsai sample index deliberately. These different field types allow different types of searches to be run against them. You can also specify analyzers, but it will default to a pre-packaged English analyzer if none is specified.

This is the command used to set up the mapping you see on the sample cluster:
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

##Create a Mapped Index


For the studio, we are going to want our airport data to be usable in Elasticsearch. Let’s take a look at our airport data and make a mapping for it. The data we have looks like this:
```nohighlight
1,"Goroka Airport","Goroka" ,"Papua New Guinea",GKA,AYGA,5282,Pacific/Port_Moresby,"Point(145.391998291 -6.081689834590001)"
2,"Madang Airport","Madang" ,"Papua New Guinea",MAG,AYMD,20,Pacific/Port_Moresby,"Point(145.789001465 -5.20707988739)"
```


Here’s a mapping we could start with for our airport data. Note -- this one will create the index AND map it in one step. You will not need to create it separately.

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




##Upload Some Data

Remember yesterday when we uploaded books?

```nohighlight
$ curl -XPOST 'localhost:9200/book/doc?pretty&pretty' -H 'Content-Type: application/json' -d '
  {
    "title": "Persuasion",
    "book_id”: “17”,
    “author_name”: “Jane Austen”
  }'
```

Let’s set one of these airports up to be imported in our new index.

```nohighlight
$ curl -XPOST 'localhost:9200/airwaze/doc?pretty&pretty' -H 'Content-Type: application/json' -d '
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
}
'
```

It might take some time to upload all large data sets, but usually that process is scripted and run automatically. Often a library is used for that.


Some Elasticsearch clients include `bulk_import`. Bulk imports are more efficient than single imports. Here’s some documentation about [bulk_import](https://www.elastic.co/guide/en/elasticsearch/reference/6.1/docs-bulk.html)
