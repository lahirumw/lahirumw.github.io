---
layout: post
title: PostgreSQL Table Partition
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Postgresql, Table Partitioning, Query Performance, Postgresql Partition, Aurora Postgresql]
author: keylearn
---

In this story we will going to discuss about table partition in PostgreSQL. Partition is splitting what is logically one large table into smaller physical pieces.

As huge amounts of data are stored in databases, performance and scaling get affected. Partitioning helps as it can divide large tables into smaller tables hence reducing memory swap problems and table scans, finally increasing the performance.

Horizontal and Vertical partition are two main categories of partitioning.

![Crepe](/assets/img/postgresql-1.webp)

**Partitioning can provide several benefits:**

1. Query performance can be improved dramatically

2. Bulk loads and deletes can be accomplished by adding or removing partitions without adding overhead

**Types of Partitions belongs to horizontal partitioning**

1. Range

2. List

3. Hash

**Important**

1. Both the primary key and all unique keys must include partition key in their definition. The partition structure itself must guarantee that there are not duplicates in different partitions

2. Partitions are not created automatically, so make sure you have a default partition

3. Ensure that the [enable_partition_pruning](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-PARTITION-PRUNING) configuration parameter is not disabled in `postgresql.conf`. If it is, queries will not be optimized as desired
4. DEFAULT partition is important feature, which stores records that don’t belongs to any other partition defined.

Let’s discuss about each type of partition in up coming stories.
Thank you.