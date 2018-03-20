---
layout: post
title: What's Redis?
tags: [Redis]
categories: Development
---

## `Redis Server`

`REDIS = Remote dictionary server`

a store/server that stores data as **`KEY-VALUE`** pairs.

<table width="50%">
  <tr>
    <th>Original Developr</th>
    <td>Salvatore Sanfilippo</td>
  </tr>
  <tr>
    <th>First Release</th>
    <td>May 10, 2009</td>
  </tr>
  <tr>
    <th>Cross Platform</th>
    <td>Written in C</td>
  </tr>
</table>

{% highlight liquid %}
e.g.

"name" = "rahul"

write -
set 'name' 'rahul' ==> name : rahul

read -
get "name" ==> "rahul"
{% endhighlight %}

### Usefull features:
* Its a No-SQL db. (Does not have - tables/rows/columns, functions etc like MySQL, Oracle DBs..)Does not uses - SELECT, INSERT, UPDATE, DELETE ..
* Uses data structures to store data (String, List, Sets, Stored Sets, Hashes etc)
* Intraction with data is command based.
* Its an in-memory db (with persistence options) - makes it superfast.

### Who's using Redis?

A list of well known companies using Redis:

* Twitter
* GitHub
* Weibo
* Pinterest
* Snapchat
* Craigslist
* Digg
* StackOverflow
* Flickr

For more details,

Reference link - https://redis.io/
