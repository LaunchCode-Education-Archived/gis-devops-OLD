---
title: "An Introduction to Docker"
---

Follow along with the instructor as you explore Docker and configure a basic Flask application.

## Setup

First, make sure that you have Docker installed.  You can check the Docker installation by running
```
$ docker --version
```

Also, make sure that you have Python and Redis installed. Mac OS X High Sierra should come with Python 2.7 installed right out of the box.  Double check by running:
```
$ python --version
```

You will need to install `pip`.  `pip` is how Python installs libraries.
```
$ sudo easy_install pip
```

Redis is a key value NoSQL database.  Install it with Homebrew.
```
$ brew install redis
$ brew services start redis
```

Double check that it is running on port `6379`.
```
$ telnet localhost 6379
```

## A simple Python web app

In this walkthrough we are going to stand up a simple Python web app.  The app uses Python Flask as a web server.  Flask may be new to you, but see if you see any similarities to Spring Boot.

Check out the [docker-flask-walkthrough]()

First let's run the file called `simple_app.py`. Be sure to install all of the app dependencies using pip first!
 ```
$ pip install --user -r requirements.tx
```

`simple_app.py`
```
import time

from flask import Flask


app = Flask(__name__)


@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

Fire up the app by running the following command:
```
$ python app.py
```

Navigating to `http://localhost:5000` should show our simple message.  Now let's turn this app into a Docker container.

We need to create a `Dockerfile` for CentOS. We'll start with the very basics and work our way up.  We'll use the `ADD` command to inject our local directory into the container at the path `/code`.

`Dockerfile`
```
FROM centos:7

ADD . /code
```

Build the image and then check to make sure that it built correctly:

```
$ sudo docker build --tag my-centos
$ sudo docker images 
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
my-centos                                       latest              ff426288ea90        8 weeks ago         207MB
```

Great. We can exactly when it was created and how large the image is.

Since the `Dockerfile` doesn't have a command to start a process, let's start the container on the terminal using the `-i -t` flags and the command `/bin/bash`.  Also, we will use the `-p 5000:5000` option to map port 5000 on the host machine to port 5000 inside the docker container.
```
$ sudo docker run -i -t -p 5000:5000 /bin/bash my-centos
$ [root@7b3cfec32b83 /]#
```

Now that we have the terminal, we can **manually** walk though the setup steps before we script them into the `Dockerfile`.

```
$ yum -y install epel-release
$ yum -y update
$ yum -y install python-pip
$ pip install --upgrade pip 
$ pip install -r /code/requirements.txt
$ python /code/simple_app.py
```

Check it out in the browser, http://127.0.0.1:5000.

Now it is time to convert our manual walkthrough into a `Dockerfile`. For any command we want to execute, we have to prepend `RUN` to the line.  For any command we want to leave running as a process, we want to append `CMD`.  Your `Dockerfile` should look like this:

```
# start with the centos 7 base image
FROM centos:7 

# add in our code
ADD . /code

# install and upgrade software
RUN yum -y install epel-release
RUN yum -y update
RUN yum -y install python-pip
RUN pip install --upgrade pip
RUN pip install -r /code/requirements.txt

# Run the web app as the main process
CMD ["python", "/code/simple_app.py"]
```

We'll need to rebuild our Docker image and relaunch the our container.
```
$ sudo docker build --tag my-centos .
$ sudo docker run -i -t -p 5000:5000 my-centos
```

Check the browser again and see if the site is still up.  

<aside class="aside-note" markdown="1">
  Note: The previous Docker container was running in the foreground.  If you want to run the Docker container as a *daemon* use the `-d` flag.
</aside>

### A more complex Python app

In the next section of the walkthrough, we are going to stand up a more complex Flask app.  In this app, we are going to integrate the key-value database Redis.  In order to integrate Redis into the Flask web app, we will need to leverage Docker's `network` capabilities.

Take a look at the `app.py`.

```
import time

import redis
from flask import Flask


app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)


def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)


@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

The code uses a Redis database to keep track of the number of visits to a particular page. Let's try and run to see the app in action:
```
$ python app.py
```

If you hit the web page http://127.0.0.1:5000 you should see the following error message:
```
Traceback (most recent call last):
  File "/Users/mikemenne/Library/Python/2.7/lib/python/site-packages/flask/app.py", line 1997, in __call__
    return self.wsgi_app(environ, start_response)
  File "/Users/mikemenne/Library/Python/2.7/lib/python/site-packages/flask/app.py", line 1985, in wsgi_app
    response = self.handle_exception(e)
  File "/Users/mikemenne/Library/Python/2.7/lib/python/site-packages/flask/app.py", line 1540, in handle_exception
    reraise(exc_type, exc_value, tb)
  File "/Users/mikemenne/Library/Python/2.7/lib/python/site-packages/flask/app.py", line 1982, in wsgi_app
    response = self.full_dispatch_request()
  File "/Users/mikemenne/Library/Python/2.7/lib/python/site-packages/flask/app.py", line 1614, in full_dispatch_request
    rv = self.handle_user_exception(e)
  File "/Users/mikemenne/Library/Python/2.7/lib/python/site-packages/flask/app.py", line 1517, in handle_user_exception
    reraise(exc_type, exc_value, tb)
  File "/Users/mikemenne/Library/Python/2.7/lib/python/site-packages/flask/app.py", line 1612, in full_dispatch_request
    rv = self.dispatch_request()
  File "/Users/mikemenne/Library/Python/2.7/lib/python/site-packages/flask/app.py", line 1598, in dispatch_request
    return self.view_functions[rule.endpoint](**req.view_args)
  File "/Users/mikemenne/Code/LaunchCode/Examples/GisDevops/docker-flask-walkthrough/app.py", line 25, in hello
    count = get_hit_count()
  File "/Users/mikemenne/Code/LaunchCode/Examples/GisDevops/docker-flask-walkthrough/app.py", line 18, in get_hit_count
    raise exc
ConnectionError: Error 61 connecting to redis:6379. Connection refused.
```
 
It looks like the app is not able to connect to `redis:6379`.  Let's do a `telnet` to check if the port is open and being listened to:
```
$ telnet redis 6379
redis: nodename nor servname provided, or not known
```

Nada... The URL `redis` doesn't even exist.  This can be fixed.  All Linux systems can use the `/etc/hosts` file to override URLs in the browser.  Open up `/etc/hosts` and add an entry for `redis`.

```
$ sudo vi /etc/hosts
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1 localhost
255.255.255.255 broadcasthost
::1             localhost 
127.0.0.1 redis
```

<aside class="aside-note" markdown="1">
  Note:  If you ever get a hold of your friend's root password, it can be good fun to mess with their `/etc/hosts` file.  You can redirect `google.com` to just about any site that you want.  
</aside>

Now if you `telnet redis 6379` it should redirect you to the `localhost` where your Redis instance is running.  Run the app again to see it in action. Each time you hit the page you should see the counter increase.

```
$ python app.py
$ curl -XGET localhost:5000
Hello World! I have been seen 1 times.
$ curl -XGET localhost:5000
Hello World! I have been seen 2 times.
$ curl -XGET localhost:5000
Hello World! I have been seen 3 times.
```

Great.  Let's make this a Docker container now. This is where Docker Compose can help us in a big way.  Docker Compose takes care of all of the `/etc/host/` changes behind the scenes so that we can just reference a container by its name.  

Here is a `docker-compose.yml` that allows the Flask app to reference the Redis container.

```
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
```

Remember, we don't have to make any changes to the `app.py` file.  Inside of the containers, Docker Compose is handling the local DNS so that any reference to `redis` gets redirected to the `redis` container.

Use the following command to stand up the two containers:
```
$ docker-compose up -d
```

You should see the app come up on `localhost:5000`. Notice that only the Python Flask server has access to the Redis server at `6379`, but you can't `telnet localhost 6379`.  This is because we did not forward the port of the Redis server.  Use the following command to forward the port of the Redis server to the local machine:

Notice also that Docker Compose is spinning up multiple containers on your behalf. Run:

```
$ sudo docker ps 

```

Let's look at these containers a bit more indepth.  `docker logs {container name}` will show all of the logs that have been written to STDOUT. (replace {container name} with the actual container name).

```
$ sudo docker logs {container name}
```

Let's also take the container details.  `docker inspect {container name}` will show all of the details about the container including network information.

```
$ sudo dockers inspect {container name}
```

```
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
    ports:
    - "6379:6379"
```

<aside class="aside-note" markdown="1">
  Be sure that the Redis server running via HomeBrew is turned of (`brew services stop redis`).
</aside>

Run `docker-compose down` to stop and remove the Docker containers.


<aside class="aside-note" markdown="1">
  If you want to remove all of your unused containers run `sudo docker container prune`
</aside>
