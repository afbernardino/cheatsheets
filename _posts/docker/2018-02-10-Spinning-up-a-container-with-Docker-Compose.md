---
title:  "Spinning up a container with Docker Compose"
date:   2018-02-10 10:54:52 -0000
categories: docker
---
<h2>Introduction</h2>
Docker Compose is usually used to define and spin up multiple containers with
just one command, but it can also be used to spin a single instance.
<br/>
<br/>
In this post I will describe how to build a docker-compose.yml and run a single
container instance, using the example of
[my previous post]({{ site.baseurl }}{% post_url /docker/2018-02-09-Spinning-up-the-first-Docker-container %}).
<br/>
<h2>The process:</h2>
1\. Create a file called "docker-compose.yml" with the following content:

{% highlight yaml %}
version: '3'   # Docker Compose version
services:
  hello-world:
    build:
      context: .    # context path
      dockerfile: Dockerfile    # Dockerfile name
    image: hello-world:custom    # the image will be created with this name and tag
    container_name: hello-world
    ports:
    - "8080:8000"

{% endhighlight %}
2\. Run the following command:
<br/>
<br/>
`docker-compose up -d`
<br/>
<br/>
Note that this will automatically build the image (if it does not already
exist), unless we explicitly state that we do not want it with `--no-build`
flag.
<br/>
This will also create a network for the services to connect to, but we won't
care about this for now, since we only have one instance running in this example.
<br/>
<br/>
You can also build the image with `docker-compose build`.
<br/>
<br/>
3\. See the container running:
<br/>
<br/>
`docker ps`
<br/>
<br/>
4\. Access to `http://localhost:8080` and see the "Hello World!" message!
<br/>
<br/>
5\. Stop the running container:
<br/>
<br/>
`docker-compose down`
<br/>
<br/>
Note that this command will automatically stop and remove the container as well
as remove the created network.
