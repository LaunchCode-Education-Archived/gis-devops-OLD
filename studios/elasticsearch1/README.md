---
title: "Elasticsearch Query Studio"
---

## ES Installation

We need to install Elasticsearch locally. Remember, ES is built on Lucene, a Java library. So you may be prompted to install a particular version of Java that is a dependency of the current version of ES.

On Mac, use Homebrew:

```nohighlight
$ brew install elasticsearch
```

And then, to start up the server:

```nohighlight
$ brew services start elasticsearch
```

Let’s check if our installation was successful by checking the cluster’s health:
```nohighlight
$ curl -XGET 'localhost:9200/_cat/health?v&pretty'
```

All being well, the output should look something like this:

```nohighlight
epoch      timestamp cluster              status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1516231703 17:28:23  elasticsearch_cheryl yellow          1         1      5   5    0    0        5             0                  -                 50.0%
```

<aside class="aside-note" markdown="1">
If you don't receive something like the response above, restart the service using: `brew services restart elasticsearch`.
</aside>

Cluster health is expressed as the color green, yellow, or red.

- Green - everything is good (cluster is fully functional)
- Yellow - all data is available but some replicas are not yet allocated (cluster is fully functional, but more vulnerable to data loss/corruption)
- Red - some data is not available for whatever reason (cluster is partially functional)

<aside class="aside-note" markdown="1">
When a cluster is red, it will continue to serve search requests from the available shards but you will likely need to fix it ASAP since there are unassigned shards.
</aside>

You can see from the output that we have 1 node and 0 shards (since there’s no data yet).
Let’s confirm we don’t have any indices/index yet.

```nohighlight
$ curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```

Your response should just show headers and no content:

```nohighlight
health status index                                    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
```

## Data Storage

Let’s create an index:

```nohighlight
$ curl -XPUT 'localhost:9200/book?pretty'
```

Now if you check for indices, you will have data:

```nohighlight
$ curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```

```nohighlight
health status index                                    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   book                                     cNIKN2CyQsacysajAuJH1A   5   1          0            0       466b           466b
```

<aside class="aside-note" markdown="1">
Don’t be concerned that your clusters or indices have yellow health in these tutorials. ES by default creates one replica for your index, but there is no second node in the cluster to assign the replica to. It is not possible to have green health without having at least one more server assigned to this cluster.
</aside>

Now that we have an index to store documents in, let’s add a document:

```nohighlight
$ curl -XPUT 'localhost:9200/book/doc/1?pretty' -H 'Content-Type: application/json' -d' { "title": "Frankenstein" }'
```

Output should tell you if the command was successful:

```json
{
  "_index" : "book",
  "_type" : "doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

It also applies an internal ID of 1. In many cases, Elasticsearch is used like a replica database where the primary is PostgreSQL or MySQL. In this case, the convention is to allow your `_id` values to be completely independent from the ID from the primary store, which is then stored like a foreign key. Remember, your cluster may be distributed among many servers, so Elasticsearch will try to use a unique hash ID that is unlikely to have collisions if a race condition between servers were to happen. You can allow ES to assign the ID by leaving it off the command.

Let’s edit our existing record. Imagine our book came from MySQL; we might add a another field like this:

```nohighlight
$ curl -XPUT 'localhost:9200/book/doc/1?pretty' -H 'Content-Type: application/json' -d
  {
    "title": "Frankenstein",
    "book_id": "7",
    "author_name": "Mary Shelley"
  }'
```

You can delete documents or indices with `-XDELETE`:

```nohighlight
$ curl -XDELETE 'localhost:9200/book?pretty'
```

If you look at the commands we have used so far, you’ll notice they’re all constructed in the same way using REST verbs. Do you see each piece of the commands? Identify to yourself the REST verb, Index name/url, ES `_id`, and JSON Content in each of our previous commands.

Go ahead and recreate our book index. (Try to remember how to type out the command before you look it up.)

## Let’s Play

Let’s put that book back in, with a couple others. This time allow ES to assign the `_id`.

```nohighlight
$ curl -XPOST 'localhost:9200/book/doc?pretty' -H 'Content-Type: application/json' -d'
  {
    "title": "Frankenstein",
    "book_id": "7",
    "author_name": "Mary Shelley"
  }'
```

```nohighlight
$ curl -XPOST 'localhost:9200/book/doc?pretty' -H 'Content-Type: application/json' -d'
  {
    "title": "The Romance of the Forest",
    "book_id": "11",
    "author_name": "Ann Radcliffe"
  }'
```

```nohighlight
$ curl -XPOST 'localhost:9200/book/doc?pretty' -H 'Content-Type: application/json' -d'
  {
    "title": "Persuasion",
    "book_id": "17",
    "author_name": "Jane Austen"
  }'
```

```nohighlight
$ curl -XPOST 'localhost:9200/book/doc?pretty' -H 'Content-Type: application/json' -d'
  {
    "title": "Middlemarch",
    "book_id": "25",
    "author_name": "George Eliot"
  }'
```

Add your favorite book too--just make up a `book_id` that is an integer that is different from the value of other other books' `book_id`.

Just in case you weren’t sure what data was in your index, let’s see if we can get the data back out. Try:

```nohighlight
$ curl -XGET 'localhost:9200/book/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "from": 1,
  "size": 10
}
'
```

Take a minute to read over the output. There is some general information about the query’s run at the top, then the resulting documents at the bottom.

Let’s try to update "Middlemarch." **Update the id-hash to use whatever showed for your local `_id`.**

```nohighlight
$ curl -XPUT 'localhost:9200/book/doc/seXDBmEBSegcuMJ-3las?pretty' -H 'Content-Type: application/json' -d'
{
  "title": "Middlemarch",
  "book_id": "25",
  "author_name": "Mary Ann Evans"
}
'
```

You should see output telling you it was successful, but just in case let’s search for that document with a `"match"` query and see what it says.

```nohighlight
$ curl -XGET 'localhost:9200/book/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": { "match": { "title": "Middlemarch" } }
}
'
```

Your results should look similar to this:

```json
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "book",
        "_type" : "doc",
        "_id" : "seXDBmEBSegcuMJ-3las",
        "_score" : 0.2876821,
        "_source" : {
          "author_name" : "Mary Ann Evans",
          "title" : "Middlemarch",
          "book_id" : "25"
        }
      }
    ]
  }
}
```

What other questions could you answer with this data? Practice writing some queries looking up books by `book_id`, by author, etc. See if you can figure out how to look up a book with a partial match (i.e. searching for `Romance` would return "The Romance of the Forest" and "Half a Lifelong Romance" by "Eileen Chang").

If you could add a field or two, what new questions could you answer? Go ahead and play with the data and test out your queries.

Want to try building another index? Check out the [awesome public datasets project](https://github.com/awesomedata/awesome-public-datasets) on GitHub.


