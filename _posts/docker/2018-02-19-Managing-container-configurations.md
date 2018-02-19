---
title:  "Managing container configurations"
description: Some methods and tools to manage container configurations.
categories: deploy
tags: docker
---
<h2>Introduction</h2>
Configurations are an important part of an application and therefore they 
are also needed in the container's world.

In this post I will describe some methods used to deal with configurations.

<h2>Methods to deal with configurations</h2>
<h3>Integrating configuration files within the image</h3>
This option allows you to have an homogeneous and standardized application,
since every instance of the application will have the same configuration files,
but on the other hand this will also force you to build an image every time a
configuration file changes.

<h3>Passing environment variables</h3>
This approach will make your container more dynamic and therefore it will lose
its homogeneity. It also means that you will have to pass the variables.

It can be done by using `-e` flag, which will map your environment variables
to inside the container.

<h3>Mapping files through bind mounts</h3>
This will map your files directly to your containers using `--mount` or `-v`
flags.

It will still able you to not build an image every time you change a
configuration file and keep your image dynamic, but it also means that you will
have to put the files on the host machine and map them properly.

<h3>Using Docker Configs and Docker Secrets</h3>
If you are using [Docker Swarm](https://docs.docker.com/engine/swarm/) you can
use [Docker Configs](https://docs.docker.com/engine/swarm/configs/) and [Docker
Secrets](https://docs.docker.com/engine/swarm/secrets/) to deploy your
configurations and secrets in a secure way, without the need to build an image 
every time you change a configuration.

Check [this post](https://diogomonica.com/2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/)
from Diogo Mónica, the Security Lead at [Docker](https://www.docker.com/), on
how this should be a better way of deploying your secrets rather than using
environment variables.

<h3>Pulling configurations inside the container at start up time</h3>
You can use a configuration management tool like [Chef](https://www.chef.io/chef/)
or [Puppet](https://puppet.com/) to pull your configurations at the time 
you start up a container.

This may not be advisable tho because you want your container to be as ligthweight 
as you can, therefore it should be small and start fast.

<h2>Conclusion</h2>
There are many ways for managing container configurations and none of them are 
mutually exclusive.

You can, for example, pull the configurations at start up time, using a 
configuration management tool, and change them afterwards using environment 
variables.
