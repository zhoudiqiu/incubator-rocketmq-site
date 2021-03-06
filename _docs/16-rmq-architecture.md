---
title: "RocketMQ Architecture"
permalink: /docs/rmq-arc/
excerpt: "The main Architecture of Apache RocketMQ."
modified: 2017-04-24T15:01:43-04:00
---


{% include toc %}


![](/assets/images/rmq-basic-arc.png)

# Overview

The greatest innovation of RocketMQ is high throughput, high reliability, and low latency of supporting massive messages through exquisite scale out and scale up. RocketMQ consists of four parts, Name Servers, Brokers, Producers and Consumers. Each of them can be horizontally extended without single Point of Failure. As shown in screenshot below.



**NameServer Cluster**

Name Servers provide lightweight service discovery and routing. Each Name Server records full routing information, provides equivalent reading and writing service, and supports fast storage expansion.

**Broker Cluster**

Brokers take care of message storage by providing lightweight TOPIC and QUEUE mechanisms. It supports the Push and Pull modes, fault tolerance mechanism (2 copies or 3 copies), strong padding of peaks and capacity of accumulating hundreds of billion messages in their original time order. In addition, Brokers provide disaster recovery, rich metrics statistics, and alert mechanisms, all of which are lack in traditional messaging systems.

**Producer Cluster**

Producers support distributed deployment. Distributed Producers send messages to the Broker cluster through multiple load balancing modes. The sending processes support fast failure and have low latency.

**Consumer Cluster**

Consumers support distributed deployment in the Push and Pull models as well. It also supports cluster consumption and message broadcast. It provides real-time message subscription mechanism and can meet most consumer scenarios. 
RocketMQ’s website provide a quick-start guide[3] for those guys who want to have a try without too much labor.

# NameServer

NameServer is a little but fully functional server, which mainly includes two features:

* Broker Management, **NameServer** accepts the register from Broker cluster and provides heartbeat mechanism to ensure whether a broker is alive.
* Routing Management, each NameServer will hold whole routing info about the broker cluster and the **queue** info for clients query.

As we know, RocketMQ clients(Producer/Consumer) will query the queue routing info from NameServer, but how do the clients find NameServer address?

There are four methods to feed NameServer address list to clients:

* Programmatic Way, like `producer.setNamesrvAddr("ip:port")`.
* Java Options, use `rocketmq.namesrv.addr`.
* Environment Variable, use `NAMESRV_ADDR`.
* HTTP Endpoint.

More details about how to find NameServer address please refer to [here](/rocketmq/four-methods-to-feed-name-server-address-list/).

# Broker Server

Broker server is responsible for message store and delivery, message query, HA guarantee, and so on.

As shown in image below, Broker server has searval important sub modules:

* Remoting Module, the entry of broker, handle the requests from clients.
* Client Manager, manage the clients (Producer/Consumer), maintain topic subscription of consumer.
* Store Service, provide simple APIs to store or query message in physical disk.
* HA Service, provide data sync feature between master broker and slave broker.
* Index Service, build index for messages by specified key and provide quick message query function.

![](/assets/images/rmq-basic-component.png)

