---
layout: post
title: Single-table inheritance vs Polymorphic association
tags: [rails ]
categories: web
---

This is a common to have join between two tables based on condition. Suppose we have table in database
called comments and we can have comments on different thing like a video, profile or picture. So keeping `DRY (Don’t repeat yourself)` principle in mind while designing your database. You may want to have relationship between single comments table and videos or profiles table.

At database level you should have two fields in table one field (i.e. `type`) that identifies the type of comment whether it is a comment on video or it’s a comment on profile, and another field (i.e `source_id`) that contains id of the video or profile that comment was on.

In Ruby on Rails currently there are two ways to define this kind of association between your active record models:

**1. Single Table Inheritance:**

Here you ‘ll create a `single table of comments` and Comment AR class, there would be two additional
classes `ProfileComment` and `VideoComment` inheriting from same Comment AR class. Instead of having association between Profile and Comment model there would be association between Profile and ProfileComment subclass. Same will be the case with Video model and VideoComment subclass.

If we take a look at comment table we ‘ll see that class name for each record will be automatically stored in type field. The reference to associated table would be in source_id field.

Here is the code structure,

{% highlight ruby %}
class Comment < ActiveRecord::Base
end

class VideoComment < Comment
  belongs_to :video, foreign_key: "source_id"
end

class ProfileComment < Comment
  belongs_to :profile, foreign_key: "source_id"
end
{% endhighlight %}

**2. Polymorphic Association:**

Since Rails 1.1 there is a simpler way to create this kind of relationship called polymorphic
association. In case of polymorphic association you ‘ll have two fields called `commentable_type` and `commentable_id`, instead of type and source_id. 

The `commentable_type` field will store name of class which that instance relates to and `commentable_id` saves the reference to instance of that class. 

The relations will be created like this:

{% highlight ruby %}
class Comment < ActiveRecord::Base
  belongs_to :commentable, polymorphic: true
end

class Video < ActiveRecord::Base
  has_many :comments, as: :commentable
end

class Profile < ActiveRecord::Base
  has_many :comments, as: :commentable
end
{% endhighlight %}

**Pros and Cons:**

- The STI approach offers more flexibilty by allowing you to have additional fields that only have value in case of one sub class. For access from other sub class can be disallowed be overridden accessors.
- The STI requires you to create a seperate class to implement a relation between two models, resulting in more classses. which is not case with polymorphic association
- The STI results in more code.It is confusing to some programmers to understand relation between STI classes.
- The polymorphic association is very easy to implement.

***

*Thanks for reading!*
