---
layout: post
title: Ruby code to fetch commit score for user on github
tags: [ruby, github ]
categories: scripting-language
---

The API endpoint to fetch the activities for a user on Github is as follow:

example -

`https://api.github.com/users/<github_username>/events/public`

The JSON response for the above call contains an area of events of different types, and
each event has a score attached to it.

The following mapping shows the scores available:

{% highlight json %}
{
"IssuesEvent" => 7,
"IssueCommentEvent" => 6,
"PushEvent" => 5,
"PullRequestReviewCommentEvent" => 4,
"WatchEvent" => 3,
"CreateEvent" => 2
}
{% endhighlight %}

Here is the a ruby program to fetch the commit score for a user on Github.

{% gist a953a88fa7e06e10dd3e24773409ba5e github_user_commit_scores.rb %}

***

*Thanks for reading!*
