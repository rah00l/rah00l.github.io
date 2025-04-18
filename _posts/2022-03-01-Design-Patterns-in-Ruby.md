---
title: Design Patterns in Ruby – Simplified with Real-World Scenarios (By Russ Olsen)
tags: [DesignPatterns, Ruby, CleanCode, LastMinutePrep]
style: border
color: danger
description: Design patterns are time-tested solutions to common problems in software design. This book simplifies classic patterns using Ruby’s expressive features like blocks, mixins, metaprogramming, and open classes. The goal is not just to learn patterns, but to understand how to apply them elegantly.
---

Design Patterns in Ruby – Simplified with Real-World Scenarios (*By Russ Olsen*)


#### 🔰 Introduction

**Design patterns** are time-tested solutions to common problems in software design. This book simplifies classic patterns using Ruby’s expressive features like blocks, mixins, metaprogramming, and open classes. The goal is not just to learn patterns, but to understand how to apply them elegantly.

<img src="{{ site.baseurl }}/public/images/design-patters-in-ruby.png" alt="Legacy Ruby Docker Image">
<br/>

#### 🏗️ Creational Patterns – Object Creation Made Flexible

##### 🔹 Singleton Pattern

**Purpose**: Ensure only **one instance** of a class exists.

**Real-World Scenario**: App configuration – we only need one configuration manager across the entire application.

**Ruby Code:**

```ruby
require 'singleton'

class AppConfig
  include Singleton
  attr_accessor :settings
end

AppConfig.instance.settings = { theme: 'dark', locale: 'en' }
```

**How it helps**: You always access `AppConfig.instance` without worrying about duplicate instances or shared state.



##### 🔹 Factory Method

**Purpose**: Delegates object creation to subclasses or decision logic.

**Use Case**: You have different types of notifications (email, SMS, push). Let the factory decide which to instantiate.

**Code Example:**

```ruby
class NotificationFactory
  def self.build(type)
    case type
    when :email then EmailNotification.new
    when :sms then SMSNotification.new
    when :push then PushNotification.new
    else raise 'Unknown type'
    end
  end
end

notif = NotificationFactory.build(:email)
notif.send("Hello")
```

**How it helps**: Decouples creation logic and centralizes it, making the code easier to scale and test.

---

### 🧱 Structural Patterns – Organizing Code for Flexibility

##### 🔹 Adapter Pattern

**Purpose**: Allows incompatible interfaces to work together.

**Real-World Use Case**: You’re integrating a third-party payment gateway, but its method names don’t match your system.

**Code:**

```ruby
class LegacyPayment
  def execute_payment(amount)
    puts "Paid #{amount} via legacy gateway"
  end
end

class PaymentAdapter
  def initialize(service)
    @service = service
  end

  def pay(amount)
    @service.execute_payment(amount)
  end
end

payment = PaymentAdapter.new(LegacyPayment.new)
payment.pay(100)
```

**How it helps**: Adapts the legacy system to your modern interface, avoiding rewriting or deep refactoring.


##### 🔹 Decorator Pattern

**Purpose**: Dynamically adds behavior to objects.

**Use Case**: You want to log or monitor service usage **without changing the original class**.

**Code:**

```ruby
class BasicNotifier
  def send(message)
    puts "Sending: #{message}"
  end
end

class LoggingNotifier
  def initialize(notifier)
    @notifier = notifier
  end

  def send(message)
    puts "[LOG] Sending message"
    @notifier.send(message)
  end
end

notifier = LoggingNotifier.new(BasicNotifier.new)
notifier.send("Hello World")
```

**How it helps**: Allows layering of responsibilities (like logging, caching, authentication) without altering core logic.

---

### 🔁 Behavioral Patterns – Managing Communication & Flow

##### 🔹 Observer Pattern

**Purpose**: One object notifies others when its state changes.

**Real Use Case**: A blog CMS that notifies subscribers when a new article is published.

**Code:**

```ruby
require 'observer'

class Blog
  include Observable

  def publish(title)
    puts "Publishing: #{title}"
    changed
    notify_observers(title)
  end
end

class Subscriber
  def update(title)
    puts "New article notification: #{title}"
  end
end

blog = Blog.new
blog.add_observer(Subscriber.new)
blog.publish("Design Patterns in Ruby")
```

**How it helps**: Decouples publisher and subscriber logic, enhancing flexibility and reusability.


##### 🔹 Strategy Pattern

**Purpose**: Encapsulates interchangeable algorithms/behaviors.

**Use Case**: Different sorting strategies in an e-commerce site (e.g., price, rating, popularity).

**Code:**

```ruby
class Sorter
  attr_writer :strategy

  def sort(items)
    @strategy.sort(items)
  end
end

class PriceSort
  def self.sort(items)
    items.sort_by { |item| item[:price] }
  end
end

class RatingSort
  def self.sort(items)
    items.sort_by { |item| -item[:rating] }
  end
end

items = [{ price: 100, rating: 4.5 }, { price: 50, rating: 4.8 }]
sorter = Sorter.new
sorter.strategy = PriceSort
p sorter.sort(items)
```

**How it helps**: Promotes clean separation of algorithms and makes your system extensible.

---

### 🧙 Metaprogramming and Patterns

**Metaprogramming** allows you to build powerful, reusable solutions like:

- Singleton creation logic
- Decorators using method wrappers
- DSLs (domain-specific languages)

**Example:** Create dynamic methods:

```ruby
class Report
  [:pdf, :csv, :html].each do |format|
    define_method("generate_#{format}") do
      puts "Generating #{format} report..."
    end
  end
end

r = Report.new
r.generate_pdf
```

**Why it matters**: Reduces repetition, increases flexibility.

---

### ✅ Best Practices

- Patterns should **simplify** your code, not complicate it.
- Avoid “pattern obsession.” Use only when it solves a real problem.
- Ruby's features often make classic patterns **easier and shorter** to implement.
- Mix and match patterns to suit your project’s needs.

---

### 🎯 Final Thoughts

Design patterns are not rigid templates. They are tools in your toolbox to write better Ruby code.

- Think in terms of **problems and solutions**.
- Start with clean code, then apply patterns if necessary.
- Let Ruby’s expressive syntax do the heavy lifting.

Want to go deeper? Re-implement these patterns using your own project. That’s how you make them stick.



