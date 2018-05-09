---
title: "Elasticsearch Query Studio"
---

## Elasticsearch Installation

We need to install Elasticsearch locally. Elasticsearch is built on Lucene, a Java library, so you may be prompted to install a particular version of Java that is a dependency of the current version of Elasticsearch.

On Mac, use Homebrew:

```nohighlight
$ brew install elasticsearch
```

And then, to start up the server:

```nohighlight
$ brew services start elasticsearch
```

Check that the installation and starteup were successful by checking the cluster’s health:
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
Let’s confirm we don’t have any indices yet.

```nohighlight
$ curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```

Your response should just show headers and no content:

```nohighlight
health status index                                    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
```

## Loading Data

Create a file `load_data.sh` and make it executable:

```nohiglight
$ touch load_data.sh
$ chmod +x load_data.sh
```

Open it in an editor and copy/paste the contents of [this gist](https://gist.github.com/chrisbay/415a961d3524fc7c91dbbf88513308d8).

Run the script to create some documents:

```nohighlight
$ ./load_data.sh
```

<aside class="aside-note" markdown="1">
If an error occurs while running the script, delete the index. Get help from an instructor and then try again.

You can delete an index this way:

```nohighlight
$ curl -XDELETE 'localhost:9200/twitter/'
```
</aside>

## Your Tasks

Now you'll get some practice with Elasticsearch. Carry out each of the following tasks. Once you have a successful query/command for each, save the commend in a `.txt` file for submission

1. Get information about the `twitter` index
1. Fetch the first 10 documents of the index
1. Fetch the last 10 documents of the index
1. Add a document to the index
1. Fetch the document that you just added
1. Delete a the document that you just added from the index
1. Get all tweets by John Smith
1. Get the average number of likes for all tweets

## Turning In Your Work

Submit your queries to an instructor via Slack once you're finished.