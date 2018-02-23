---
title:  "Spinning up the first Docker container"
description: "Process to spin up a Docker container."
categories: deploy
tags: docker
---
<h2>Introduction</h2>
This is Cheat Sheets so I will just give a rather short introduction to Docker
containers.

Containers allow you to deploy an application on any machine regardless its
environment, which means they will run the same way on every host machine.

They differ from virtual machines because they do not need to be shipped with
an entire OS or hardware abstraction tools (that a virtual machine requires),
which makes them very lightweight.

Still they are also isolated from the surrounding environment, by making use of
namespaces and cgroups, which are a part of the kernel, that determine what
processes can see and how much they can use.

In this post I will describe the difference between an image and a container
as well as the process to build an image and spin up a container. I will also
talk about the cleaning up process.

<h2>Image and container</h2>
To understand the following steps you need to understand the difference between
an image and a container.
Think of an image as a box, where you just put your application (and its
dependencies) inside, and the container as the running instance of what is
inside that box. The box is what you ship and what is inside the box is what
you use.

<h2>Build the image</h2>
1\. Create a directory for your image:

{% highlight shell %}
mkdir <directory name>
{% endhighlight %}

2\. Change path to your directory:

{% highlight shell %}
cd <directory name>
{% endhighlight %}

3\. Create a file called "helloworld.py" with the following content:

{% highlight python %}
from http.server import HTTPServer
from http.server import BaseHTTPRequestHandler
from http import HTTPStatus

class MyHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(HTTPStatus.OK)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        self.wfile.write(b'Hello World!')
        return

def run(server_class=HTTPServer, handler_class=MyHandler):
    server_address = ('0.0.0.0', 8000)
    httpd = server_class(server_address, handler_class)
    try:
        print("Server is up and running!")
        httpd.serve_forever()
    except KeyboardInterrupt:
        print("Server will be stopped and connection closed!")
        httpd.socket.close()

if __name__ == '__main__':
	run()
{% endhighlight %}

This is a simple program written in Python that will display a page with "Hello
World!" at the following address: 0.0.0.0:8000.

Note that this is a Python program but it could be anything!

4\. Create a file called "Dockerfile" with the following content:

{% highlight shell %}
# base image
FROM python:rc-alpine

# set entry working directory
WORKDIR /usr/src/app

# copy app to the image
COPY ./helloworld.py /usr/src/app

# expose port to access the app
EXPOSE 8000

# command to run
CMD [ "python", "./helloworld.py" ]
{% endhighlight %}

5\. Build the image:

{% highlight shell %}
docker build -t <image name>:<tag> <context path>
{% endhighlight %}

Where `<image name>` is the name you want for the new image, `<tag>` is the tag
that you want to associate with the image (if you do not provide a tag it will
be "latest") and `<context path>` the path where your files to be inserted in
the image and Dockerfile are (in this case the path to your created directory).

This will pull `python:rc-alpine` image from
[Docker Hub](https://hub.docker.com/) registry to your local registry, and
execute the following commands.

6\. Check the image was created:

{% highlight shell %}
docker images
{% endhighlight %}

<h2>Spin up the container</h2>
1\. Spin up the container:

{% highlight shell %}
docker run -d -p <host port>:8000 --name <container name> <image name>:<tag>
{% endhighlight %}

Where `-d` stands for detached, which means your container will run in the
background, `-p` the port mapping, being the left side of `:` the port of the
host machine and the right side the port of the container (the one you exposed),
`<container name>` the name of the container that will be created,
`<image name>` the name of the image from which you want to spin up your
container (the one you created) and `<tag>` the tag of the image from which you
want to spin up your container.

2\. See the container running:

{% highlight shell %}
docker ps
{% endhighlight %}

3\. Access to `http://localhost:<host port>` (being `<host port>` the host port
mapped to the container) and see the "Hello World!" message!

<h2>The cleaning up process</h2>
1\. Stop the container:

{% highlight shell %}
docker stop <container name>
{% endhighlight %}

2\. Check if the container is no longer running:

{% highlight shell %}
docker ps -a
{% endhighlight %}

3\. Remove the container:

{% highlight shell %}
docker rm <container name>
{% endhighlight %}

4\. Check if the container was removed:
{% highlight shell %}
docker ps -a
{% endhighlight %}
