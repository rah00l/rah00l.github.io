---
layout: post
title: Why to use Redis?
tags: [Redis]
categories: Development
---

Let us disscuss some features/advantages of redis,

#### Performance/speed:
- Redis is `super fast`.
- As per the Benchmark guide provided, The performance of Redis go upto 120k Requests/second and even more.(see the screenshot)

<img src="{{ site.url }}/public/images/redis_request_per_seconds_chart.png"/>

#### Simple and Flexible:
- Its a `No-SQL db`, no need of defining tables/rows/columns,functions etc like MySQL, Oracle DBs..)
- No need of SQL statements SELECT, INSERT, UPDATE, DELETE ..
- Data read/write simple and stright forword

#### Durable:
- Redis has option to write data to disk.
- Data write options are configurable.
- It can be used as a `Caching System` or a `Full fledged DB`.

#### Language and Platform support:
Redis can support a lot many langauges and platforms listed as below.

<img src="{{ site.url }}/public/images/redis_languages_support.png"/>

Refere Redis official client page to know more. https://redis.io/clients

#### Compatibility:
- Redis can be used as `second database` along with your exising database to make your transactions and execution of query faster.
- Requests which needs to be serviced very fast and frequent can be put into redis memory and rest of data can be lia into your main DB.
- It service the request from Cache if data is available in Cache, or Get the data from db if data is not avaialble in Cache. 

#### Scaling:
- Redis have very good `Master Slave Replication Feature`.
- It has different number of redis instances and we can make any one of it as a master and others are slaves. Master can be used for write only data store, whereas one of the slave can used for read only and other slaves used to write data to the disks asynchronously, whcich increaes the performance (performance optimization) and No downtime.

#### Other Features:
- Has a single text file for all configurations.
- It is `single Threaded` - one action at a time.
- `Piplelinig` - Where you can cluster multiple commands and send them at once it make redis even more faster.
