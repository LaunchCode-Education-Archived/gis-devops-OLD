---
title: Walkthrough
---



Bonsai's hosted ES service provides an Elasticsearch cluster that we can practice queries against.  The URL is:

https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net

<aside class="aside-note" markdown="1">
Free Bonsai clusters must sleep for 8 hours out of every 24. [Bonsai's documentation](https://docs.bonsai.io/docs/sleeping-clusters?utm_swu=9264&utm_source=sendwithus&utm_content=sandbox-welcome-email-v01&utm_medium=email&utm_campaign=heroku-sandbox-emails) explains how these accounts work.

Please don't use this server between 11pm-7am CT so it's ready for class.
</aside>




## Let's Start!

Let's see if everything is well with this cluster before we begin:

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/_cat/health?v&pretty'
```

Output should look like this:

```nohighlight
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent

1516559163 18:26:03  elasticsearch green           3         3      0   0    0    0        0             0                  -                100.0%
```

Cluster health is expressed as the color green, yellow, or red.

- Green - everything is good (cluster is fully functional)
- Yellow - all data is available but some replicas are not yet allocated (cluster is fully functional, but more vulnerable to data loss/corruption)
- Red - some data is not available for whatever reason (cluster is partially functional)

Cool, now let's see what indices are available.

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/_cat/indices?v&pretty'
```

```nohighlight
health status index                  uuid                   pri rep docs.count docs.deleted store.size pri.store.size

green  open   book E_jXjglKRNertzCBvONueg   1   1          0            0       260b           130b
```

Looks like we have some information on books. To start, let's get a small sample of documents to see what kind of structure is here:

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/book/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} }
}'
```

Output:
```nohighlight
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "hits" : {
    "total" : 101,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "book",
        "_type" : "doc",
        "_id" : "AWEcOSQaVQBSxP80hZw8",
        "_score" : 1.0,
        "_source" : {
          "title" : "The Romance of the Forest",
          "author_name" : "Ann Radcliffe",
          "year" : 1791,
          "description" : "A beautiful, orphaned heiress, a dashing hero, a dissolute, aristocratic villain, and a ruined abbey deep in a great forest are combined by the author in a tale of suspense where danger lurks behind every secret trap-door.",
          "book_id" : 6,
          "location" : [
              48.8566,
              2.3522
            ]
          }
        }
      },

```
[some output omitted]


If you don't specify the number of results to be returned, ES will return 10 documents. You can request a certain number of documents by setting the size.

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/book/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "from": 1,
  "size": 3
}'
```



## Text Search

Let's search for books authored by anyone named "Ann". What are our options for this?

We already did a `"query"` clause, so let's try to run with that.

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/book/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match" : { "author_name" : "Ann" }
    }
}'
```

Success!


```nohighlight
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 3.9693582,
    "hits" : [
      {
        "_index" : "book",
        "_type" : "doc",
        "_id" : "AWEcRP4zuXhGZDLBpfY0",
        "_score" : 3.9693582,
        "_source" : {
          "title" : "The Romance of the Forest",
          "author_name" : "Ann Radcliffe",
          "year" : 1791,
          "description" : "A beautiful, orphaned heiress, a dashing hero, a dissolute, aristocratic villain, and a ruined abbey deep in a great forest are combined by the author in a tale of suspense where danger lurks behind every secret trap-door.",
          "book_id" : 6,
          "location" : [
              48.8566,
              2.3522
            ]
          }
        }
      }
    ]
  }
}
```

For the rest of these exercises, assume we are using the same curl command and we will only show the hash in the request body to save space and make this easier to read.

Now… what about 'Anne'? How can we get that spelling too?

Let's just try passing both spellings

```nohighlight
{
    "query" : {
        "match" : { "author_name" : "Ann Anne" }
    }
}
```

Nope, this is the response you get when there is no match:

```nohighlight
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "hits" : {
    "total" : 0,
    "max_score" : null,
    "hits" : []
  }
}
```

What if we just searched for either name?

```nohighlight
{
  "query": {
    "bool": {
      "should": [
        { "match": { "author_name": "Ann" } },
        { "match": { "author_name": "Anne" } }
      ]
    }}}
```

Success!! It's returning every book with an author name containing either 'Ann' or 'Anne'. `"Should"` is like `“OR”` (any one of the clauses must match), whereas `“must”` is like `“AND”` (both clauses would have to be present). You can use `"must_not"` instead of `"must"` if you want to filter negatively instead of positively, that is, remove some from a set and return the remainder.

```nohighlight
  "hits" : {
    "total" : 3,
```

[some output omitted]

That's pretty tedious though, huh? We would really prefer that Elasticsearch figure out this misspelling business. Let's try one of those 'fuzzy' queries we talked about earlier.

```nohighlight
{
    "query" : {
        "fuzzy" : { "author_name" : 'Ann" }
    }
}'
```

Huh that didn't work. Turns out there are some defaults that reduce the amount of letter changes allowed for very short words. Let's try manually overriding the defaults.  [Elasticsearch documentation](https://www.elastic.co/guide/en/elasticsearch/reference/6.1/common-options.html#fuzziness) can tell you everything you need to know about how to do this.

```nohighlight
{
    "query": {
        "fuzzy" : {
            "author_name" : {
                "value": "ann",
                "boost": 1.0,
                "fuzziness": 2,
                "prefix_length": 0,
                "max_expansions": 100
            }}}}
```

Whoa that worked. Now there are seven total results, in order: one Ann, two Annes, a Fanny, and then a Jane and a few other that don't really make sense. This could use some more tuning, but since it sorts the most useful to the top, it's good enough for now.

```nohighlight
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "hits" : {
    "total" : 7,
    "max_score" : 3.487692,
    "hits" : [
      {
        "_index" : "book",
        "_type" : "doc",
        "_id" : "AWEcRP4zuXhGZDLBpfY0",
        "_score" : 3.487692,
        "_source" : {
          "title" : "The Romance of the Forest",
          "author_name" : "Ann Radcliffe",
```

[some response output omitted]

We could also do similar searches for words in the book description field, but let's move on to some other types of searches for now.



## Range Filter

Often you will want to use one type of search clause in combination with another. Here, we are using a `"match_all"` to get a bunch of records, but filtering out books with the year prior to 1900. We are also eliminating those 2000 and after, but you will notice this collection does not include any books that recent.

```nohighlight
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "year": {
            "gte": 1900,
            "lte": 2000
          }}}}}}
```

```nohighlight
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "hits" : {
    "total" : 70,
    "max_score" : 1.0,
    "hits" : [
```

[some content omitted]

Looks like that's working well! There are 70 hits… but wait! If you look at the `hits.hits` array, only 10 objects are included. Recall, that is the default number of records Elasticsearch will return. You can set `"from"` and `"size"` on each query to control the size of the array returned, and which result to begin from. It always tells us the total count of results in the response.

```nohighlight
{
 "from": 30,
 "size": 10,
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "year": {
            "gte": 1900,
            "lte": 2000
          }}}}}}
```

## Aggregations

Aggregations allow us to get statistics and information about groups of documents, while returning the documents at the same time if you need to. What kind of questions like this could we ask about this book data? All these books have a year associated with them (either the year written, published, or an approximation of that). What year has the most books in it?

```nohighlight
{
  "size": 0,
  "aggs": {
    "group_by_year": {
      "terms": {
        "field": "year"
      }}}}
```

Well, it's a three-way tie, but at least our agg worked. It's definitely giving us insights into our data.

```nohighlight
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "hits" : {
    "total" : 100,
    "max_score" : 0.0,
    "hits" : []
  },
  "aggregations" : {
    "group_by_year" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 72,
      "buckets" : [
        {
          "key" : 1940,
          "doc_count" : 4
        },
        {
          "key" : 1948,
          "doc_count" : 4
        },
        {
          "key" : 1953,
          "doc_count" : 4
        },
        {
          "key" : 1960,
          "doc_count" : 3
        },
```

[some output omitted]


## Geo Distance Query

Searching by location is a powerful feature in Elasticsearch. You'll notice the first third or so of the books in the collection have location information stored in the form of a latitude and longitude pair.

Let's see if we can figure out which books were written near St. Louis. A quick Google search of "St. Louis coordinates" returned 38.6270° N, 90.1994° W, or [90.1994, 38.6270]. If you enter that in maps, it looks like it's actually set to St. Louis City Hall.

```nohighlight
$ curl -XGET 'https://ekyqz8nza5:6gz15xze7h@elasticsearch-traini-2142321757.us-east-1.bonsaisearch.net/book/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query": {
        "bool" : {
            "must" : {
                "match_all" : {}
            },
            "filter" : {
                "geo_distance" : {
                    "distance" : "200km",
                    "location" : { "lon": 90.1994, "lat": 38.6270
                    }}}}}}'
```

Oops, got 0 hits. Now you may have noticed that these locations don't make any sense. I looked up coordinates for somewhere that the book took place or the author was from, and then I entered several in backwards. Elasticsearch supports several ways to enter in this data, and I strongly suggest you label yours rather than using an array or string.

```nohighlight
"location": {
  "lat": 41.12,
  "lon": -71.34
}
```

Let's try this again, but with an arbitrary location close to one of our books.

```nohighlight
{
    "query": {
        "bool" : {
            "must" : {
                "match_all" : {}
            },
            "filter" : {
                "geo_distance" : {
                    "distance" : "100km",
                    "location" : { "lon": 138, "lat": 36
                    }}}}}}
```

Successful Output [most omitted]:

```nohighlight
"hits" : {
    "total" : 1,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "book",
        "_type" : "doc",
        "_id" : "AWEmhv7RVQBSxP80ho-T",
        "_score" : 1.0,
        "_source" : {
          "title" : "The Tale of Genji",
          "author_name" : "Murasaki Shikibu",
          "year" : 1020,
          "description" : "Genji, the Shining Prince, is the son of an emperor. He is a passionate character whose tempestuous nature, family circumstances, love affairs, alliances, and shifting political fortunes form the core of this magnificent epic.",
          "book_id" : 2,
          "location" : [
            138.2529,
            36.2048
          ]
```

