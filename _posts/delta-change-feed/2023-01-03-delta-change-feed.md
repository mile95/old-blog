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

Assume the following delta table with two measurement sources.

| id | pos       |
|----|-----------|
| x  | (100,100) |
| y  | (200,200) |

The change feed of the above delta table could look this

| id | operation | pos       | commit_version |
|----|-----------|-----------| -------------- |
| x  | Insert    | (100,0)   | 1              |
| y  | Insert    | (200,200) | 1              |
| x  | Update    | (100,100) | 2              |

The above change feed indicates that we have two measurement sources `x` and `y`. 
Measurement source `x` has two versions (since the position had been updated) while `y` only has one. 
The commit version identifies a specific point in time at which the data in the table was committed.
It points to a version of the table containing the change.

Recently we identified a bug in our data pipeline where all expected versions were not identified correctly due to the way we consumed the change feed.

## The misunderstanding

We consumed the change feed in a streamed manner using pyspark to easy faciliate checkpoints of which rows of the change feed we had processed and which we had not processed. 

```python
sources = spark.readStream \
    .format("delta") \
    .option("readChangeFeed", "true") \
    .load("<path-to-delta-table>")
```

For the above example, our solution worked fine.
The issue arised when we had a change feed that we **expected** to look something like the table below.

| id | operation | pos       | commit_version |
|----|-----------|-----------| -------------- |
| x  | Insert    | (100,0)   | 1              |
| y  | Insert    | (200,200) | 1              |
| x  | Update    | (100,100) | 2              |
| x  | Update    | (100, 0)  | 3              |

Here the position of measurement source `x` has been reverted back to `(100,0)`, meaning that the last and first version of `x` are equal in terms of position.
Running the above code resulted in this minimal change feed.

| id | operation | pos       | commit_version |
|----|-----------|-----------| -------------- |
| x  | Insert    | (100,0)   | 3              |
| y  | Insert    | (200,200) | 3              |

Hence, the version where `x` had position `(100,100)` and measurement source `y`, was swept under the carpet resulting in a lossed measurement sources and versions.

We realised that the issue was due to the fact that we streamed the change feed.
The reason to why we streamed the change feed was 
The [documentation](https://docs.databricks.com/delta/delta-change-data-feed.html) states; 

*"To get the change data while reading the table, set the option readChangeFeed to true. The startingVersion or startingTimestamp are optional and if not provided the stream returns the latest snapshot of the table at the time of streaming as an INSERT...*"

The received change feed is therefore expected since it contains the latest snapshot of the delta table as an INSERT statement.
Atleast for me, this is not very intuative, I would expect that I would recevive the complete change feed.

## The solution

To counter to the misunderstanding was to 