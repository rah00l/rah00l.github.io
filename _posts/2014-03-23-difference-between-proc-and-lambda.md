---
layout: post
title: Difference between Procs and Lambdas
tags: [ruby ]
categories: scripting-language
---

It is important to mention that they are **both Proc objects**.

{% highlight ruby %}

proc = Proc.new { puts "Hello world" }
lam = lambda { puts "Hello World" }

proc.class # returns 'Proc'
lam.class  # returns 'Proc'

{% endhighlight %}

* Lambdas `check the number of arguments`, while procs do not
{% highlight ruby %}
lam = lambda { |x| puts x } # creates a lambda that takes 1 argument

lam.call(2) # prints out 2
lam.call # ArgumentError: wrong number of arguments (0 for 1)
lam.call(1,2,3) # ArgumentError: wrong number of arguments (3 for 1)
{% endhighlight %}

In contrast, procs don’t care if they are passed the wrong number of arguments.

{% highlight ruby %}
proc = Proc.new { |x| puts x } # creates a proc that takes 1 argument

proc.call(2) # prints out 2
proc.call # returns nil
proc.call(1,2,3) # prints out 1 and forgets about the extra arguments
{% endhighlight %}

As shown above, procs don’t freak out and raise errors if they are passed the wrong number of arguments. 

If the proc requires an argument but no argument is passed then the proc returns nil. If too many arguments are passed than it ignores the extra arguments.

* Lambdas and procs treat the `‘return’ keyword differently`

**‘return’ inside of a lambda** triggers the code right outside of the lambda code

{% highlight ruby %}
def lambda_test
  lam = lambda { return }
  lam.call
  puts "Hello world"
end

lambda_test # calling lambda_test prints 'Hello World'
{% endhighlight %}

**‘return’ inside of a proc** triggers the code outside of the method where the proc is being executed

{% highlight ruby %}
def proc_test
  proc = Proc.new { return }
  proc.call
  puts "Hello world"
end

proc_test # calling proc_test prints nothing
{% endhighlight %}

***

*Thanks for reading!*
