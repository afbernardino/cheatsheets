---
title:  "Quick tips on images and layers"
description: "How image layers work and how to manage them."
categories: deployment
tags: docker devops
---
<h2>Introduction</h2>
In [this post]({{site.baseurl}}{% post_url /2018-02-09-Spinning-up-the-first-Docker-container %})
I have described the difference between an image and a container, how to build
an image and how to spin up a container.

In this post I will focus on the images and briefly describe how the layered
system used by Docker works and give some tips about it.

<h2>How it works</h2>
Docker images work on a layered system. This means that all the single changes
you make to an image will add an extra layer to the image.
<figure>
    <img src="{{ site.baseurl }}/assets/img/docker-layers.png" alt="Docker layers" width="600" height="400"/>
    <figcaption>Figure 1. Docker layers</figcaption>
</figure>
Figure 1 was actually taken from the official Docker documentation website.

As you can see the image layers are only readable layers and you can only write
to container layers.

It means that you can have multiple containers reading from the same image,
without the need of copying the whole content of an image every time you run a
container.

To be able to achieve this Docker uses Copy-On-Write (COW) strategy, which means
that a resource is copied to the container layer only when it is modified. This
is very important for performance and memory space.

<h2>First tip: Building the image</h2>
Each step of the building process adds a new layers to the image, which means
you can try to put the the most changing items at the bottom of your Dockerfile
to get faster build times, like in this example:

{% highlight dockerfile %}
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

As you can see the copy of "helloworld.py" takes place at the bottom of
Dockerfile, because it is the most changing item. This allows you to not
recreate the same layer over and over again and therefore allows you faster
build times.

Bare in mind that a Dockerfile should be easy to read and easy to understand, so
try to reconcile both worlds.

<h2>Second tip: The "< none > images"</h2>
These called "`< none >` images" are actually layers and there are two types of
"`< none >` layers": The "good" ones and the "bad" ones.

Starting with the "good" ones, when you pull an image you pull all of its layers
and they are all linked to each other, Docker only gives a name to the last one.
These are the ones you see when you run `docker images -a`.

The "bad" ones (so called dangling images), are the ones left when you recreate
an image (remember first tip). They are called dangling images because they are
not linked to anything.

You can get rid of them by running the following command:

{% highlight shell %}
docker rmi $(docker images --quiet --filter=dangling=true)
{% endhighlight %}

If you do not want the error message when there are no dangling images you can
run the following command (this will depend on your OS):

{% highlight shell %}
docker images --quiet --filter=dangling=true | xargs --no-run-if-empty docker rmi
{% endhighlight %}

Use the following to force:

{% highlight shell %}
docker images --quiet --filter=dangling=true | xargs --no-run-if-empty docker rmi -f
{% endhighlight %}
