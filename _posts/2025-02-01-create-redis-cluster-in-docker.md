---
layout: post
title: Create a Redis Cluster in Docker
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Redis, Redis Cluster, Redis Cache, Cache, Docker]
author: keylearn
---

Redis is a popular open-source, in-memory key-value store that is known for its high performance and flexibility. It’s often used to cache and manage data in various applications. When working with Redis in a production environment, creating a Redis Cluster can be a critical step to ensure high availability and data redundancy. In this blog, we will walk you through the process of creating a Redis Cluster in Docker, allowing you to experiment and develop with a distributed Redis setup in a single host for testing purpose.

Before you get started, ensure you have the following prerequisites:

1. Docker: Make sure you have Docker installed on your machine.

2. Basic Docker knowledge: Familiarize yourself with Docker commands and concepts if you are not already.

**Let’s set up the cluster step by step.**

First, open your terminal and create docker network with the following command.

~~~
docker network create redis-net
~~~

To ensure that Redis containers within the cluster can communicate with each other, create a dedicated Docker network.

You need at least six Redis nodes to create a Redis Cluster. In this example, we will create a cluster with three master nodes and three slave nodes. You can modify the configuration as needed. Let’s create below redis.conf file in the project directory.

~~~
port 6379
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
~~~

These settings in the Redis configuration file are essential for configuring Redis in a cluster setup, ensuring that it listens on the correct port, uses cluster mode, specifies the cluster configuration file, sets a node timeout, and enables data persistence through append-only files.

~~~
docker run -d -v ./redis.conf:/usr/local/etc/redis/redis.conf --name redis-1 --net redis-net redis redis-server /usr/local/etc/redis/redis.conf
docker run -d -v ./redis.conf:/usr/local/etc/redis/redis.conf --name redis-2 --net redis-net redis redis-server /usr/local/etc/redis/redis.conf
docker run -d -v ./redis.conf:/usr/local/etc/redis/redis.conf --name redis-3 --net redis-net redis redis-server /usr/local/etc/redis/redis.conf
docker run -d -v ./redis.conf:/usr/local/etc/redis/redis.conf --name redis-4 --net redis-net redis redis-server /usr/local/etc/redis/redis.conf
docker run -d -v ./redis.conf:/usr/local/etc/redis/redis.conf --name redis-5 --net redis-net redis redis-server /usr/local/etc/redis/redis.conf
docker run -d -v ./redis.conf:/usr/local/etc/redis/redis.conf --name redis-6 --net redis-net redis redis-server /usr/local/etc/redis/redis.conf
In the above commands, We are using official Redis Docker image to spin up the containers. we’re creating three master nodes and three slave nodes.
~~~

Now that you have the nodes running, you need to configure them into a Redis Cluster. You can use the redis-cli tool to initialize the cluster. Connect to one of the Redis container:

~~~
docker exec -it redis-1 /bin/bash
~~~

Then, use the following command to configure the cluster:

~~~
redis-cli --cluster create \
  redis-1:6379 redis-2:6379 redis-3:6379 \
  redis-4:6379 redis-5:6379 redis-6:6379 \
  --cluster-replicas 1
~~~

In the above command, we specify the host and port of each Redis node and the number of replicas (slaves) for each master node. The --cluster-replicas 1 flag indicates that each master node will have one replica.

Once you execute the command you will see the output like below attachment.

![Crepe](/assets/img/redis-1.webp)

To verify that your Redis Cluster is up and running, use the following command:

~~~
redis-cli -c -h 172.18.0.2 -p 6379 cluster nodes
~~~

This command will display information about the cluster nodes, such as their IDs, IP addresses, and status.

Creating a Redis Cluster in Docker allows you to experiment with distributed Redis setups in a controlled environment. You can further customize your Redis Cluster by adding more nodes, adjusting the number of replicas, and exploring various Redis configurations. This guide provides a foundation for setting up a basic Redis Cluster, but you can expand upon it to meet your specific needs and use cases. Enjoy working with your Redis Cluster in Docker!
