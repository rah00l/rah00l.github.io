---
layout: post
title: Remove failed jobs from resque
tags: [rails, resque ]
categories: web
---

Lets know how to remove failed or errors from `SampleJob (a custom job)` without affecting other Jobs.

Here is the script,

{% gist d8e2cc726f701c73dc73e37853b7b01b remove_failed_jobs.rb %}

With the help of this code we can very eassily remove failed custom resque job.
