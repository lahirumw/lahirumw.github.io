---
layout: post
title: PostgreSQL Hash Table Partition
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: []
author: keylearn
---

This story is a part of [PostgreSQL Table Partition](https://lahirumw.github.io/2025-02-09-postgresql-table-partition/). Let’s talk about hash partition in this section. Hash partition is kind of a horizontal partition.

Hash partition supports PostgreSQL 11+ versions.
Hash partition table aren’t storing similar data like range partition, rather it partitioned by specifying a modulus and a remainder for each partition.

If you need a three partitions using hash, then you must set the modulus as three like example below.

Now execute the following script.

~~~
--Table
CREATE TABLE IF NOT EXISTS activities
(
    act_id int NOT NULL,
    act_name VARCHAR(255) NOT NULL,
    source VARCHAR(255) NOT NULL,
    created_date date NOT NULL
) PARTITION BY HASH (act_id);

-- Partitions
CREATE TABLE IF NOT EXISTS activities_1 PARTITION OF activities
    FOR VALUES WITH (MODULUS 3, REMAINDER 0);

CREATE TABLE IF NOT EXISTS activities_2 PARTITION OF activities
    FOR VALUES WITH (MODULUS 3, REMAINDER 1);

CREATE TABLE IF NOT EXISTS activities_3 PARTITION OF activities
    FOR VALUES WITH (MODULUS 3, REMAINDER 2);
~~~

Once script is executed, you will see the three partitions for activities table.