---
title: 🐙 Ruby’s Dynamic Single Dispatch Explained
tags: [Ruby, OpenClasses, Refinements, ruby-core-lab, QuickNotes]
style: border
color: danger
description: In Ruby, method calls are resolved dynamically at runtime** based on the receiver’s class — this is known as dynamic, single dispatch.
---

In Ruby, **method calls are resolved dynamically at runtime** based on the **receiver’s class** — this is known as **dynamic, single dispatch**.

Here’s what that means and why it matters:

### 🔧 How Ruby Finds and Calls Methods

* **Single Dispatch**
  Ruby picks the method to run based **only on the object before the dot** (the receiver).
  It *does not* consider the argument types.

  > Unlike languages with *double dispatch*, where both receiver and arguments matter.

* **Method Lookup Path**
  Ruby checks the receiver’s class first, then its included modules and ancestors, up the inheritance chain, until it finds a matching method.

* **Dynamic Dispatch**
  You can call methods dynamically using:

  ```ruby
  obj.send(:greet, "world")
  ```

  * `send` bypasses visibility (can call private methods).
  * `public_send` respects visibility rules.

* **Define Methods at Runtime**
  Ruby lets you add methods dynamically:

  ```ruby
  define_method(:dynamic_hello) { puts "Hello from runtime!" }
  ```

* **Fallback with `method_missing`**
  If no method is found, Ruby calls `method_missing`, which you can override to handle unknown methods gracefully.

---

### ⚡ Quick Example: Single Dispatch in Action

```ruby
class Animal
  def speak
    "generic sound"
  end
end

class Dog < Animal
  def speak
    "woof"
  end
end

pet = Dog.new
puts pet.speak  # => "woof"
```


<br/>

<img src="{{ site.baseurl }}/public/images/rubys-method-dispatch-lookup-path.png" alt="Modern Rails Architecture">  
<br/>


Even if you pass different arguments, Ruby always chooses `Dog#speak` based only on the receiver’s class.

---

### 🛠 Performance & Limits

* Ruby interpreters (like JRuby, TruffleRuby) use **inline caches** to speed up repeated method lookups.
* Ruby **does not** support method overloading — redefining a method name overwrites the old one.
* Using `send` with unchecked input can introduce **security risks**.

---

✅ **In short:**
Ruby’s method dispatch is:

* **Dynamic** → decided at runtime
* **Single** → based only on the receiver’s class
* **Flexible** → supports metaprogramming (`send`, `define_method`, `method_missing`)


✏️ Quick Recap
* ✅ Method chosen at runtime, based only on the receiver
* ✅ Looks up through class, modules, ancestors
* ✅ Supports send, define_method, method_missing
* ✅ No method overloading by argument types
* ✅ Dynamic but can be slower; modern Ruby uses caches to speed it up

✨ That’s why Ruby feels so flexible: you can change behavior at runtime, build DSLs, or intercept calls — all thanks to dynamic single dispatch.

---


