---
title: "Elasticsearch Studio 2"
---

Time for you to have an opportunity to practice on your own. We’re going to be setting up Elasticsearch in AWS, indexing data from our project, and writing a few queries in our project.

## Setting Up AWS Elasticsearch Service

Sign in to the AWS console. 

<aside class="aside-note" markdown="1">
Your instructor should have provided you with a free AWS account. If you don't have access to one yet, talk to them.
</aside>

Click on *Services* and under *Analytics* click on *Elasticsearch Service*. Click on the blue button that says *Create a new domain*.

On the *Define domain* page, for *Elasticsearch domain name*, type a name for the domain. In this studio, we use the domain name **airwaze** with a dash and your name (e. g. **airwaze-me**). Leave *Version* set to the newest supported version. Click *Next*.

On the *Configuration* page, let’s leave everything at the default values. Click *Next*.

On the *Set up access* page, in the *Network configuration* section, choose *Public Access*. (There may be an error window; after you click *Public Access*, you will be able to close that window and everything should be fine.) Under *Access Policy*, click *Allow open access to the domain*. We don’t recommend you do this with production applications, especially sensitive data. More information about AWS and access policies will be covered later. Click *Next*.

On the *Review* page, review your domain configuration, and then choose *Confirm*.

New domains take up to ten minutes to initialize. After your domain is initialized, you can upload data and make changes to the domain.

## Index Airwaze Data in Elasticsearch

You need to set up a data mapping in your new index and import the data.

Get the `elasticsearch-starter` branch from the [Airwaze repo](https://gitlab.com/LaunchCodeTraining/airwaze-studio) on Gitlab. Change the filepath in the import.sql file to point to your own airports and routes. We’ve written a little script for you to index the data from the `Airport.csv`. Take a look at `upload-airports.rb` in the root directory of your project. As you can see, the script is reading in the airport file one line at a time, manipulating the data into the correct format, and using cURL to index the data, just like we’ve been doing it manually.

You can test this script against your local Elasticsearch cluster:

```nohighlight
$ ruby upload-airports.rb
```

It does take several minutes to run, and it fails on a few of the airports (some are not formatted correctly in the csv), but this is ok for demonstration purposes.

<aside class="aside-note" markdown="1">
The ruby script is slow because it is submitting one request at a time.  ElasticSearch also has a bulk API that allows you to upload hundreds or thousands of records at once.  Feel free to try it out.  Here's a Node library that makes it easy to import CSV files: [elastic-import](https://www.npmjs.com/package/elastic-import).
</aside>

Create the airwaze index and upload the document mapping in your new AWS ES domain. You can follow the instructions from the walkthrough, changing `localhost:9200` with your new AWS ES endpoint and changing out *airwaze* for the name of your new domain (such as *airwaze-me*).

In the script file, comment out the line where the script is using your localhost. Uncomment the other `host_name` line, and replace `your_url_here` with the endpoint for your AWS Elasticsearch domain when it is ready. It should look something like this:

```nohighight
host_name = “https://search-airwaze-some-long-hash.us-east-2.es.amazonaws.com”
#host_name = “localhost:9200”
```

Then, in this line:
`test = Kernel.system "curl -XPOST '#{host_name}/airwaze/doc?pretty&pretty' `
replace "airwaze" with the name of your domain (again, like `airwaze-me`).

Once you’ve confirmed the index is ready to receive data, in the terminal in the project’s directory, run the revised script. When it’s done running, now check out that document count in the index.

```nohighlight
$ curl -XGET 'replace this with your aws endpoint here/_cat/indices?v&pretty'
```

Output is something like this:

```nohighlight
health status index                                    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   airwaze-me                                B7lSfnmJR0OdUH6LQlKBKw   5   1       7119            0      1.5mb          1.5mb
yellow open   book                                     cNIKN2CyQsacysajAuJH1A   5   1          4            0     19.3kb         19.3kb
```

## Add Elasticsearch.js

Now that we have all that juicy data in Elasticsearch, let’s allow users to query against that data via a web form. What questions might our users have about this data? How can we answer them with the tools we have shown you?

We’re going to start by using Elasticsearch.js, a client-side library for ES maintained by Elasticsearch. To install it, first make sure you have [node and npm](https://www.npmjs.com/get-npm) installed.

[Download](https://download.elasticsearch.org/elasticsearch/elasticsearch-js/elasticsearch-js-14.1.0.tar.gz) the zip package and unzip it in the project directory `src/main/resources/static` folder.

Next, add this line to your `index.html` file to load up the es.js browser build package. Make sure that this line is added after the jQuery source.

```noghighlight
<script src="elasticsearch-js/elasticsearch.jquery.min.js"></script>
```

In `src/main/resources/static/script.js` insert this code to create a client: 

```nohighlight
let client = new $.es.Client({
  hosts: 'localhost:9200',
  log: 'trace'
});
```

Build and run your project. In the browser's Dev Tools, go to Sources, js, and check that the file contents are there. Now look at the console and you should see what’s referred to as a CORS error.

```nohighlight
Failed to load http://localhost:9200/: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:8080' is therefore not allowed access.
```

## Managing CORS

Learn more about [CORS online](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

So we are having trouble making this request in the browser because it is coming from a different port number and therefore it’s treated as potentially dangerous like it was from a different server. We need to configure Elasticsearch to allow this behavior. Let’s investigate our current settings first.

Navigate to the folder where Elasticsearch is installed on your computer. On Mac, that’s likely `/usr/local/etc/elasticsearch`. You should see a file `elasticsearch.yml` there. Add this configuration to the file:

```nohighlight
http.cors.enabled : true
http.cors.allow-origin : "*"
http.cors.allow-methods : OPTIONS, HEAD, GET, POST, PUT, DELETE
http.cors.allow-headers : X-Requested-With,X-Auth-Token,Content-Type, Content-Length
```

We need to restart Elasticsearch for this to take effect.

```nohighlight
$ brew tap homebrew/services
$ brew services list
$ brew services restart elasticsearch
```

In the browser console, you should see a message like this if everything worked:

```nohighlight
INFO: 2018-01-28T22:29:52Z
  Adding connection to http://localhost:9200/
```

Kudos! Now change that “\*” to be your actual information.

## Queries

Now that we can connect to Elasticsearch, let’s write a query! This library provides a more succinct syntax for queries, as do most of the others. Let’s start with a basic query just to see what this particular library’s syntax looks like. Put this in your `script.js` file underneath the `let client…` block.

```nohighlight
    client.search({
        index: 'airwaze',
        body: {
            query: { "match_all": {}},
            size: 10
        }
    }).then(function (resp) {
        let hits = resp.hits.hits;
        console.log(hits);
    }, function (err) {
        console.trace(err.message);
    });
```

Rebuild and reload in the browser, and you should see a response in the console with an Object like this:

![response object](/../../materials/week03/responseObject.png)

Good, we can work with that.

### Search by Name

Writing out the query by hand, you would probably come up with something like this:

```nohighlight
$ curl -XGET 'localhost:9200/airwaze/_search?pretty' -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match" : { "name" : "Midway" }
    }
}'
```

Let’s alter our test query from above to look for an airport by name:

```js
    client.search({
        index: 'airwaze',
        body: {
            query: { "match": {
                “name”: “Midway”
            }},
            size: 10
        }
    }).then(function (resp) {
        let hits = resp.hits.hits;
        console.log(hits);
    }, function (err) {
        console.trace(err.message);
    });
```

Check this query like we did before (rebuild and reload in browser) and you will see the response has changed.

![The response has changed](/../../materials/week03/infoObject.png)

### Name Search UI

Now that we have the data, let’s set up a basic UI so the users can interact with search. We’ll add a label, a text box, and a button to submit. We won’t be using a full form here, we’ll just use a jquery function on button click so the user will not have to wait on a round trip to the server or a page reload.

First, in `index.html` add the form elements. I’ve used bootstrap to split the screen with the `#airportList` element and added a little padding below the map to make it easier to click in the box.

```html
<br />
<div class="row">
    <div class="col-md-6">
        <div id="airportList">
        </div>
    </div>
    <div class="col-md-6">
        <div id="search">
            <label>Name Search</label>
            <input name="name" type="text" id="nameTextbox">
            <button type="button" id="nameSearchButton">Submit</button>
        </div>
    </div>
</div>
```

In the `script.js` file, add this below the block where the es client is defined. Make sure it is enclosed within the document ready function, or else the function might not bind to the element properly.

```js
    $("#nameSearchButton").on( "click", function() {
        let name = document.getElementById('nameTextbox').value;
        console.log("Searching for " + name);
        client.search({
            index: 'airwaze',
            body: {
                query: {
                    "match": {
                        "name": name
                    }
                },
                size: 10
            }
        }).then(function (resp) {
            let hits = resp.hits.hits;
            console.log(hits);
        }, function (err) {
            console.trace(err.message);
        });
    });
```

Now stop and restart your project and check it out in the browser. When you reload, make sure to hold down <shift> in addition to the usual <command>-R to make sure the browser retrieve fresh assets. Let’s search for `Lambert` and see what comes up to test it.

![what comes up for a Lambert search](/../../materials/week03/airportMap.png)

Cool! But our users aren’t going to use the console to see their results. Let’s show the data in the airport list on the left side. Change the response of our on click function.

```js
        }).then(function (resp) {
            let hits = resp.hits.hits;
            console.log(hits);
            $('#airportList').empty();
            $('#airportList').append(`<div id="airportList"><h3>${hits[0]._source.name}</h3></div>`);
```

##Extra Credit:

When a user selects an airport by clicking on it in the map, more information than just the name is displayed in the Airport List. Add additional airport fields to the document mapping and import so that the user sees the same information no matter how they selected the airport.

There are other JS functions happening when a user selects an airport by clicking on it. Refactor the JS so that those functions can be called either by the click or by search top hit. You can copy/paste as part of your refactoring process, but the end result should not have the function duplicated anywhere.

How does the user know which airport they have selected? Add some kind of indicator of the currently selected airport on the map. (Perhaps fill in the circle.) This function should toggle on/off when the user changes their selection by clicking or searching.
Show multiple search results. Make each item in the list clickable so the user can highlight on the map via their search results.
Add additional search(es). What other questions might the user have about this data? Come up with some questions, write another query, then add to the UI to provide the user with the answer. Examples might be:

- Which airports are within X distance of me? You could use a select dropdown to provide the user with distance choices that are likely to return results. Make sure that your response display shows them which airport and what distance they had searched for.
- How far are airports from each other? A distance query, you’d also need to provide a way to select a second airport.
- (Your idea here)

