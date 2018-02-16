---
layout: post
title: Setup Rspec testing for a simple Ruby script
tags: [Rspec, Ruby]
categories: Development
---

Discussing the simplest use-case that to Setup Rspec testing to a simple Ruby script.

We use `rspec` a **Behaviour Driven Development for Ruby**.

### The Gemfile

We assume you have already installed `Ruby Gems and Bundler`. Next we create our Gemfile with:

{% highlight ruby %}
source 'https://rubygems.org'
gem 'rspec'
gem 'rake'
{% endhighlight %}

We can create rvm gemset for this ruby script but it's optional.

And then we install the gems as follows:

{% highlight liquid %}
bundle install
{% endhighlight %}

We can create `spec` directory to have our testcase files and configuration file into it.

### The spec helper

In order to configure and load ruby script file path we have to add following code to `spec\spec_helper.rb` file.

{% highlight ruby %}
root_dir = File.expand_path("../", File.dirname(__FILE__))
Dir[root_dir + "**/*.rb"].each { |file| require file }
{% endhighlight %}

## The ruby script
Imagine we have a script that just calls a method which ultimate returns a string message. We add this script in `sample_code.rb` file:

{% highlight ruby %}
class SampleCode
 def test
   "Inside test method"
 end
end
{% endhighlight %}

### The first test case
Now we will add the test case, an expectation that our method, if passed returns a string message.

{% highlight ruby %}
require "spec_helper"
describe SampleCode do
  context "#test" do
     it "should say 'Inside test method' when we call test method" do
        message = SamppleCode.new.test
        expect(message).to eq "Inside test method"
     end
  end
end
{% endhighlight %}

When you run test case by `rspec` command it runs and pass the written simple test case.

This is how we can setup rspec testing of a simple Ruby script.
