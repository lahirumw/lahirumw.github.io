---
layout: post
title: PostgreSQL List Table Partition
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [List Partition, Postgresql, Table Partitioning, Query Optimization, Postgres Partition]
author: keylearn
---

This story is a part of [PostgreSQL Table Partition](https://lahirumw.github.io/2025-02-09-postgresql-table-partition/). Let’s talk about list partition in this section. List partition is kind of a horizontal partition.

In this type partitions created by using list of values. As an example, a table called promotion has a column which is category. “clothes”, “foods”, “fashion”, and “travel” are some of the values in category. As there are huge number of records in the table, so you can partition table by category as a partition key. This will allow us to divide the table logically in to each category and improve the query performance.

Partition key — Determines which partition is used for data.

Partition bounds — List of values for partition.

Constraints — Each partition has its own indexes and constraints.

Now execute the following script.

~~~
-- Table
CREATE TABLE IF NOT EXISTS promotions
(
    id uuid NOT NULL,
    category VARCHAR(255) NOT NULL,
    title VARCHAR(255) NOT NULL,
    created_date date NOT NULL,
    CONSTRAINT promotions_pkey PRIMARY KEY (id, category)
) PARTITION BY LIST (category);

-- Partitions
CREATE TABLE IF NOT EXISTS promotions_default PARTITION OF promotions
    DEFAULT;

CREATE TABLE IF NOT EXISTS promotions_fashion PARTITION OF promotions
    FOR VALUES IN ('cosmetics', 'clothes');

CREATE TABLE IF NOT EXISTS promotions_travel PARTITION OF promotions
    FOR VALUES IN ('travel');
~~~

Like above example we can use list partition when logical data group into subgroups.