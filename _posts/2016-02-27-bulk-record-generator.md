---
layout: post
title: Rails Generate 1 Million Records in 20 minutes
tags: [rails, activeRecord ]
categories: database
---

I love ActiveRecord. It does so many things so well. Hiding horribly verbose SQL queries behind concise, readable ruby; magically knowing that the foreign key for `has_many :media` is `medium_id`; replacing  garbage like **“PG::UniqueViolation: ERROR: duplicate key value violates unique constraint ‘index_users_on_email’”** with the perfectly end-user-readable `“Email address has already been taken”`; 

Of course, the inevitable price for this abstraction is some performance overhead. Usually it’s not a bottleneck, but when you need to do something just slightly more complicated than usual, it can be. 

In order to verify performance issue we need to have few million records, and problem araises  that on our development machines we do not have that much amount of data. We have to generate bulk records to verify issues related to performance.

Following script is usefull to generate bulk amount of db records for `Book` model which is having `has_many` association with `FileSubmissions` and `FileSubmissions` `has_one` kind of association to `FileDetail`.

{% gist 3610ef1aa18b587bcd34 bulk_record_generator.rb %}

With the help of this code we can very eassily generate bulk amount of records.

***

*Thanks for reading!*
