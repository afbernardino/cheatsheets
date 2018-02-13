---
title:  "Orchestrating multiple containers and manage their configurations"
description: >
  Some methods and tools to orchestrate multiple containers and manage their
  configurations.
categories: deploy
tags: docker devops
---
<h2>Introduction</h2>
Scaling our application by increasing the number of containers means that we
will have more moving stuff to manage and it can become very hard without the
proper tools.

In this post I will start by describing some of the methods used to deal with
configurations and then share some tools that can help us managing the system.

<h2>Methods for dealing with configurations</h2>
<h3>Copying configuration files while building the image</h3>
This option allows you to have an homogeneous and standardized application,
since every instance of the application will have the same configuration files,
but on the other hand this will also force you to build an image every time a
configuration file changes.

<h3>Passing environment variables</h3>
This approach will make your container more dynamic and therefore it will lose
its homogeneity. It also means that you will have to pass the variables.

<h3>Mapping files through bind mounts</h3>
This will map your files directly to your containers using the `--mount` or `-v`
flag.

It will still able you to not build an image every time you change a
configuration file and keep your image dynamic, but it also means that you will
have to put the files on the host machine and map them properly.

<h3>Using Docker's own tools</h3>
If you are using [Docker Swarm](https://docs.docker.com/engine/swarm/) you can
use [Docker Configs](https://docs.docker.com/engine/swarm/configs/) and [Docker
Secrets](https://docs.docker.com/engine/swarm/secrets/) to deploy your
configurations and secrets in a secure way.

Check [this post](https://diogomonica.com/2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/)
from Diogo Mónica, the Security Lead at [Docker](https://www.docker.com/), on
how this should be a better way of deploying your secrets rather than using
environment variables.

<h2>Tools that can help</h2>
<h3>Docker Swarm</h3>
As said before [Docker Swarm](https://docs.docker.com/engine/swarm/) is
[Docker](https://www.docker.com/)'s own tool and it is used to orchestrate
multiple containers.

* It uses the master-worker architecture and it has its own overlaying network
and load balancer;
* It uses [Docker Configs](https://docs.docker.com/engine/swarm/configs/) and
[Docker Secrets](https://docs.docker.com/engine/swarm/secrets/) to deal with
configurations and secrets;
* Very easy to set up and use, since it is well integrated and the documentation
is also very good.

<h3>Kubernetes</h3>
[Kubernetes](https://kubernetes.io/) is another orchestration tool for
containers.

* It uses the concept of pods which are small groups of containers;
* Can make use of [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/);
* It was designed by Google and it is widely used to orchestrate containers in a
large scale.

<h3>Ansible</h3>
[Ansible](https://www.ansible.com/) is a configuration management and
application deployment software.

* It uses playbooks that contain tasks, which are essentially used to provide a
state to a target machine;
* It uses masters nodes to connect to target hosts. The master nodes then
retrieve the state from each target host and run necessary tasks;
* The connection is made through SSH;
* Playbooks are written in YAML and are stored in master nodes;
* It is very easy to start with and blazing fast to set up, since it just needs
to be installed on master machines and configured SSH keys to start.

<h3>Puppet</h3>
[Puppet](https://puppet.com/) is also another software configuration management
tool.

* It uses its own domain-specific language (DSL) to describe system
configuration;
* It uses agent-master architecture;
* The master nodes have the catalog with the needed resources (each resource
describes some aspect of a system, like a specific service or package);
* Agents periodically connect to masters to send facts and request a catalog and
if any agent finds any resource that are not in the their desired state, it will
make the necessary changes.

<h3>Chef</h3>
[Chef](https://www.chef.io/chef/) is yet another configuration management tool.

* It uses a DSL built on top of Ruby, which makes it very powerful.
* It uses Chef DK Workstation, Chef server and Chef client nodes to run;
* Chef client nodes ask Chef server for configurations (Cookbooks) and apply
necessary changes.
* Chef client nodes can be used inside containers to retrieve the system/container
configurations and apply necessary changes, although it is not advisable because
you want your container to start as fast as you can and the container should be
as small as it can be.

<h2>Conclusion</h2>
There are many tools that can help us managing configurations as well as
orchestrate our fleet of containers.

In my opinion although configuration managing tools like
[Ansible](https://www.ansible.com/), [Puppet](https://puppet.com/) and
[Chef](https://www.chef.io/chef/) can be a workable  solution for small
deployments I feel that [Docker Swarm](https://docs.docker.com/engine/swarm/)
and [Kubernetes](https://kubernetes.io/) are more powerful on the orchestration
side for large deployments.

This being said it does not mean that they can not be used together.

There are, for example, implementations with [Ansible](https://www.ansible.com/)
being used to deploy a [Docker Swarm](https://docs.docker.com/engine/swarm/) as
well as implementations that use [Puppet](https://puppet.com/) or
[Chef](https://www.chef.io/chef/) to initialize container's configurations
while [Kubernetes](https://kubernetes.io/) is being used to orchestrate the
fleet.
