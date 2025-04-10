---
layout: post
title: Delegates in Rails with simple example
tags: [Ruby ]
categories: Rails
style: fill
color: danger
description: In Rails, delegates allow you to forward method calls from one object to another.
---


In Rails, delegates allow you to forward method calls from one object to another. This is useful when you want to delegate the responsibility of handling certain methods to another object. Let's illustrate this with a simple example:


Suppose you have a `User` model and an `Address` model, and each user has an associated address. You want to be able to access address attributes directly from the user object without having to explicitly call the address object each time. This is where delegates come in handy.

Here's how you can use delegates:

```ruby
class User < ApplicationRecord
  has_one :address

  # Delegate methods from the address object to the user object
  delegate :city, :state, :zipcode, to: :address
end

class Address < ApplicationRecord
  belongs_to :user
end
```

In this example, we have a `User` model with a `has_one :address` association, indicating that each user has one associated address. We then use the `delegate` method to delegate the `city`, `state`, and `zipcode` methods from the address object to the user object.

Now, you can access address attributes directly from a user object:

```ruby
user = User.first
puts user.city
puts user.state
puts user.zipcode
```

Behind the scenes, Rails will forward these method calls to the associated address object, making it appear as if the user object itself has these methods. This helps keep your code clean and concise by avoiding unnecessary method chaining and delegation logic.