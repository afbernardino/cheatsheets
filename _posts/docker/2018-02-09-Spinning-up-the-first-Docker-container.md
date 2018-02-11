---
title:  "Spinning up the first Docker container"
date:   2018-02-09 14:27:52 -0000
categories: docker
---
<h2>Introduction</h2>
This is Cheat Sheets so I will just give a rather short introduction to Docker
containers.
<br/>
<br/>
Containers allow us to deploy an application on any machine regardless its
environment, which means they will run the same way on every host machine.
<br/>
They differ from virtual machines because they do not need to be shipped with
an entire OS or hardware abstraction tools (that a virtual machine requires),
which makes them very lightweight.
<br/>
Still they are also isolated from the surrounding environment, by making use of
namespaces and cgroups, which are a part of the kernel, that determine what
processes can see and how much they can use.
<br/>
<br/>
In this post I will describe the difference between an image and a container
as well as the process to build an image and spin up a container. I will also
talk about the cleaning up process.
<br/>
<h2>Image and container</h2>
To understand the following steps we need to understand the difference between
an image and a container.
Think of an image as a box, where you just put your application (and its
dependencies) inside, and the container as the running instance of what is
inside that box. The box is what you ship and what is inside the box is what
you use.
<br/>
<h2>Build the image</h2>
1\. Create a directory for your image:
<br/>
<br/>
`mkdir <dir name>`
<br/>
<br/>
2\. Change path to your directory:
<br/>
<br/>
`cd <dir name>`
<br/>
<br/>
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
<br/>
Note that this is a Python program but it could be anything!
<br/>
<br/>
4\. Create a file called "Dockerfile" with the following content:
{% highlight bash %}
# base image
FROM python:rc-alpine

# set entry working directory
WORKDIR /usr/src/app

# expose port to access the app
EXPOSE 8000

# command to run
CMD [ "python", "./helloworld.py" ]

# copy app to the image
COPY ./helloworld.py /usr/src/app
{% endhighlight %}
<br/>
5\. Build the image:
<br/>
<br/>
`docker build -t <image name>:<tag> <context path>`
<br/>
<br/>
Where `<image name>` is the name you want for the new image, `<tag>` is the tag
that you want to associate with the image (if you do not provide a tag it will
be "latest") and `<context path>` the path where your files to be inserted in
the image and Dockerfile are (in this case the path to your created directory).
<br/>
<br/>
This will pull `python:rc-alpine` image from
[Docker Hub](https://hub.docker.com/) registry to your local registry, and
execute the following commands.
<br/>
<br/>
6\. Check the image was created:
<br/>
<br/>
`docker images`
<br/>
<h2>Spin up the container</h2>
1\. Spin up the container:
<br/>
<br/>
`docker run -d -p <host port>:8000 --name <container name> <image name>:<tag>`
<br/>
<br/>
Where `-d` stands for detached, which means your container will run in the
background, `-p` the port mapping, being the left side of `:` the port of the
host machine and the right side the port of the container (the one we exposed),
`<container name>` the name of the container that will be created,
`<image name>` the name of the image from which you want to spin up your
container (the one we created) and `<tag>` the tag of the image from which you
want to spin up your container.
<br/>
<br/>
2\. See the container running:
<br/>
<br/>
`docker ps`
<br/>
<br/>
3\. Access to `http://localhost:<host port>` (being `<host port>` the host port
mapped to the container) and see the "Hello World!" message!
<br/>
<h2>The cleaning up process</h2>
1\. Stop the container:
<br/>
<br/>
`docker stop <container name>`
<br/>
<br/>
2\. Check if the container is no longer running:
<br/>
<br/>
`docker ps -a`
<br/>
<br/>
3\. Remove the container:
<br/>
<br/>
`docker rm <container name>`
<br/>
<br/>
4\. Check if the container was removed:
<br/>
<br/>
`docker ps -a`
