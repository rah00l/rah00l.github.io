---
layout: post
title: Ruby programs
tags: [Ruby, Program]
categories: Ruby
---

### Ruby programs

## Hash string keys convert it to integer

{% highlight ruby %}
example_hash = { '003' => 'Royal Bank of Canada', '265' => 'Deutsche Bank AG', '314' => 'JP Morgan Bank Canada', '354' => 'Jameson Bank' }

Hash[ example_hash.keys.map(&:to_i).zip(example_hash.values) ]

OUTPUT:
{3=>"Royal Bank of Canada", 265=>"Deutsche Bank AG", 314=>"JP Morgan Bank Canada", 354=>"Jameson Bank"}

{% endhighlight %}