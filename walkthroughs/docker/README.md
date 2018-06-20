---
title: "An Introduction to Docker"
---

Follow along with the instructor as you explore Docker and configure a basic Flask application.

## Docker Commands
- `docker ps` see list of **running** containers
- `docker ps -a` see lisf of all containers, including ones that failed or were stopped
- `docker start <container-name or id>` starts the container
- `docker stop <container-name or id>` stops the container
- `docker restart <container-name or id>` restarts the container
- `docker rm <container-name or id>` removes the container
- `docker images` shows list of images that you have downloaded. containers are created from images
- `docker image rm <image-name or id>` removes an image
- `docker build --tag super-happy-fun-os .` builds a new image that containers can be created from
- `docker-compose up -d` uses a docker-compose.yml file to configure and start containers
- For more info and more commands please see [the Docker CLI docs](https://docs.docker.com/engine/reference/commandline/docker/)

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

### Setup
Clone this repo [docker-flask-walkthrough](https://gitlab.com/LaunchCodeTraining/docker-flask-walkthrough)

Install dependencies using pip by running:
 ```
$ pip install --user -r requirements.txt
```

Review file `simple_app.py`
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

Run the simple web app by running the below command
```nohighlight
$ python simple_app.py
```

Navigate to `http://localhost:5000`, it should show our simple message.  Please **stop** this process as we will next get this running via a Docker container.

### Create Dockerfile
We need to create a `Dockerfile` for CentOS. Add the below content into `/docker-flask-walkthrough/Dockerfile`. Don't just copy and paste it, review each line.

`Dockerfile`
```
# start with the centos 7 base image
FROM centos:7 

# ADD <source> <destination>, Adds the current directory to /code in the container
ADD . /code

# install and upgrade software we need on the container
RUN yum -y install epel-release
RUN yum -y update
RUN yum -y install python-pip
RUN pip install --upgrade pip
RUN pip install -r /code/requirements.txt

# Run the web app as the main process (there can only be one CMD per Dockerfile)
CMD ["python", "/code/simple_app.py"]
```

We need to build a Docker image that will run our simple web app. Run the below commands in the root of `/docker-flask-walkthrough`
```
$ docker build --tag my-centos-simple .
$ docker create -i -t -p 5000:5000 my-centos-simple
$ docker start <container_name/id>
```

Check the browser to see if the "Hello World" message shows up. `http://localhost:5000`

### A more complex Python app

In the next section of the walkthrough, we are going to stand up a more complex Flask app.  In this app, we are going to integrate the key-value database Redis.  In order to integrate Redis into the Flask web app, we will need to leverage Docker's network capabilities.

Review `counter_app.py`:
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


@app.route('/counter')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

The code uses a Redis database to keep track of the number of visits to a particular page. Let's try and run to see the app in action:
```
$ python counter_app.py
```

If you hit the web page `http://localhost:5000/counter` you should see the following error message:
```
Traceback (most recent call last`):
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

**OPE**... The host `redis` doesn't exist.  This can be fixed locally by changing `host='redis'` to `host='127.0.0.1'` in `counter_app.py`.

Then run the app again `$ python counter_app.py` and navigate to `http://localhost:5000/counter` and see "Hello World!!! I have been seen 19 times."

### Linking Containers
We don't want our users to have to install redis on their own. We need to create a container that runs redis. Then we can link the `redis` and `counter-app` containers using `docker-compose`. Sounds fun right?

### How to Find the Redis Image
Go to [Docker Hub](https://hub.docker.com/) and search for `redis`. Click on the official `redis` result. Click the **tags** tab. We are going to reference the `redis:alpine` tag. That refers to a specfic version of redis, details are available on the docker site.

Pull in a copy of the `redis:alpine` image to your computer by running `$ docker pull redis:alpine`

### Create counter-app Image
1. Stop your local `redis` by running `brew services stop redis`
2. Change the last line in the `Dockerfile` to run the `counter_app.py`. Change to this `CMD ["python", "/code/counter_app.py"]`
3. Set `host='redis'` in `counter_app.py`
4. Build the `centos-counter-app` image: `$ docker build --tag centos-counter-app .`
 - The above command takes a while to run. After it completes you will see the below message:
```noghighlight
Successfully built 8447bcee9c62
Successfully tagged centos-counter-app:latest
```
5. Verify it was built by viewing images `$ docker images`

### Docker Compose File
We are going to bring this all together by creating  a `docker-compose.yml` file, that will allow the Flask app to reference the Redis container.

```
version: '3'
services:
  web:
    image: "centos-counter-app"
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
```

Use the following command2 to stand up and verify the two containers:
1. Input the above YAML into `docker-compose.yml`
2. Run `$ docker-compose up -d`
```
  Creating docker-flask-walkthrough_redis_1 ... done
  Creating docker-flask-walkthrough_web_1   ... done
```
3. Verify that the containers are running `$ docker ps`
4. Naviage to `http://localhost:5000/counter`

Remember that your local Redis is no longer running. There is a web app conatiner running that has a connection to the redis container.


### Docker Logs
Let's look at these containers a bit more indepth.  `docker logs {container name}` will show all of the logs that have been written to STDOUT. (replace {container name} with the actual container name).

```
$ docker logs {container name/id}
```

Let's also take the container details.  `docker inspect {container name/id}` will show all of the details about the container including network information.

```
$ docker inspect {container name/id}
```