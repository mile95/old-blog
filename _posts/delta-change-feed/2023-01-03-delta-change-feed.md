---
title: "Delta tables change feed pitfalls"
date: 2023-01-03T10:48:19+02:00
tags: [data analysis, python]
author: Fredrik Mile
---

Delta tables are a type of data management system that is used to store and track data changes over time.
They are optimized for handling large amounts of data and for performing efficient data updates.
Delta tables are typically used in data warehousing and analytics applications, and are often integrated with other data management systems such as Apache Spark.

In a project I recently worked in, we used a delta table to store measurement sources.
We utalized the change feed of the delta table to identify new or old versions of a measurement source.

The change feed of delta table is a record of all the changes made to the table over time.
For instance, if the change feed contained an `Insert`, that meant that a new measurement source had been identified. An `Update` meant that we had a new version of the measurement source, for instance if the measurement source had changed its position.
We used the change feed of the table to keep track of versions of the measurement sources.

Assume the following delta table with two data sources.

| id | pos       |
|----|-----------|
| x  | (100,100) |
| y  | (200,200) |

The change feed of the above delta table could look this

| id | operation | pos       |
|----|-----------|-----------|
| x  | Insert    | (100,0)   |
| x  | Update    | (100,100) |
| y  | Insert    | (200,200) |

The above change feed indicates that we have two measurement sources `x` and `y`. 
Measurement source `x` has two versions (since the positions had been updated) while `y` only has one.

Recently we identified a bug in our data pipeline where all expected versions were not identified correctly due to the way we consumed the change feed.
Before I go through the actual issue I need to cover the different way one can consume the change feed.

## SQL History

## Pyspark Batch Reading

## Pyspark Stream

