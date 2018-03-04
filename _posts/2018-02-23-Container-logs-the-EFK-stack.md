---
title:  "Container logs (the EFK stack)"
description: EFK stack for managing and storing container logs.
categories: deployment
tags: docker fluentd elasticsearch kibana
---
<h2>Introduction</h2>
Logs let you know what is going on with your application and there are several
methods for you to manage and store logs from your containerized applications.

In this post I will shortly explain how Elasticsearch, Fluentd and Kibana (EFK)
stack works and provide you a simple example.

<h2>How it works</h2>
The EFK stack has three components:
* [Elasticsearch](https://www.elastic.co/products/elasticsearch): which stores
your logs.
* [Fluentd](https://www.fluentd.org/): which routes and parses your logs.
* [Kibana](https://www.elastic.co/products/kibana): which lets you analyze your
logs through graphs and more.

<figure>
    <img src="{{ site.baseurl }}/assets/img/efk-stack.png" alt="ELK stack" width="700" height="200"/>
    <figcaption>Figure 1. EFK stack</figcaption>
</figure>

As Figure 1 demonstrates, your containerized applications send their logs,
through Fluentd's driver, to Fluentd server, where logs are routed and parsed,
and then Fluentd server sends the logs to Elasticsearch, where they are stored.

After this your logs are ready to be visualized through Kibana.

And that's it for the EFK stack! You can find a sample
[here](https://github.com/afbernardino/efk-sample).
