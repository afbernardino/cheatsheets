---
title:  "Spinning up a container with Docker Compose"
description: "Process to spin up a container with Docker Compose."
categories: deployment
tags: docker devops
---
<h2>Introduction</h2>
Docker Compose is usually used to define and spin up multiple containers with
just one command, but it can also be used to spin a single instance.

In this post I will describe how to build a docker-compose.yml and run a single
container instance, using the example of
[my previous post]({{ site.baseurl }}{% post_url /2018-02-09-Spinning-up-the-first-Docker-container %}).

<h2>The process:</h2>
1\. Create a file called "docker-compose.yml" with the following content:

{% highlight yaml %}
version: '3'   # Docker Compose version
services:
  hello-world:
    build:
      context: .    # context path
      dockerfile: Dockerfile    # Dockerfile name
    image: hello-world:custom   # the image will be created with this name and tag
    container_name: hello-world
    ports:
      - "8080:8000"

{% endhighlight %}
2\. Run the following command:

{% highlight shell %}
docker-compose up -d
{% endhighlight %}

Note that this will automatically build the image (if it does not already
exist), unless you explicitly state that you do not want it with `--no-build`
flag.

This will also create a network for the services to connect to, but you don't
need to worry about this for now, since you only have one instance running in
this example.

You can also build the image with `docker-compose build`.

3\. See the container running:

{% highlight shell %}
docker ps
{% endhighlight %}

4\. Access to `http://localhost:8080` and see the "Hello World!" message!

5\. Stop the running container:

{% highlight shell %}
docker-compose down
{% endhighlight %}

Note that this command will automatically stop and remove the container as well
as remove the created network.
