---
title: "Performance of AWS S3 Buckets"
date: 2024-06-30T17:21:32+02:00
aliases: ["/aws-s3-performance"]
tags: ["AWS", "Performance"]
author: "Marco Rehmer"
draft: false
summary: "Read and write performance of Amazons S3 Buckets"
cover:
  image: "images/aws-s3-logo.png"
  alt: "AWS S3 Bucket logo"
---

S3 buckets are ideal for storing data that rarely changes but is frequently accessed. For example, to store large amounts of data, an S3 bucket can be a suitable choice because the performance for reading and writing scales with the demands.

## Standard Performance

When accessing the S3 bucket or objects within the bucket in the regular way, the S3 bucket can deliver the following performance values:

- **3,500** requests per second for functions like PUT/COPY/POST/DELETE
- **5,500** requests per second for functions GET/HEAD

This is already decent performance but can quickly become a bottleneck if thousands of clients want to read data very quickly, or a lot of data is being written (e.g., metrics from millions of clients).

## Partitioning with Prefixes

Object prefixes can help here: they allow objects in the S3 bucket to be partitioned, practically infinitely increasing access performance. **Each partition** brings the performance mentioned above. So if the objects are divided into 10 partitions, you achieve a performance of **35,000 / 55,000 requests per second**. That is incredibly fast!

The number of prefixes within the bucket is unlimited, so with the right data organization, the desired performance can be achieved, and it is up to the application architecture to reach this.

### Using Prefixes

The use is very simple: a prefix is merely the beginning of an object name. Since it is possible to store objects with various special characters, delimiters such as `/`, `-` or `.` can also be used.

So, if you want to store data from all cities in the world in your S3 bucket, the prefix could look like this:

- Europe/France/Nouvelle-Aquitaine/Bordeaux
- North America/Canada/Quebec/Montreal
- North America/USA/Washington/Seattle
- North America/USA/Washington/Bellevue

When accessing, the desired partition can now be restricted using a delimiter and prefix.

To output data from North America, specify `Delimiter='/'` and `Prefix='North America/USA/'` for the partition, and you will reach the corresponding data.
