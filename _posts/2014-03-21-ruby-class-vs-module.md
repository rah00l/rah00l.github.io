---
layout: post
title: Ruby Class vs Module
tags: [ruby ]
categories: scripting-language
---


Ruby modules allow you to create groups of methods that you can then include or mix into any number of classes. `Modules only hold behaviour, unlike classes, which hold both behaviour and state.`

Since a module cannot be instantiated, there is no way for its methods to be called directly. Instead, it should be included in another class, which makes its methods available for use in instances of that class.

Here are the quick pointers of exact difference between a class and module.

<table>
  <thead>
    <tr>
      <th></th>
      <th>Class</th>
      <th>Module</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>instantiation</b></td>
      <td>can be instantiated</td>
      <td>can <b>not</b> be instantiated</td>
    </tr>
    <tr>
      <td><b>usage</b></td>
      <td>object creation</td>
      <td>mixin facility, provide a name space.</td>
    </tr>
    <tr>
      <td><b>super class</b></td>
      <td>module</td>
      <td>object</td>
    </tr>
    <tr>
      <td><b>consists of</b></td>
      <td>methods, constants and variables</td>
      <td>methods, constants and classes</td>
    </tr>
    <tr>
      <td><b>methods</b></td>
      <td>class methods, instance methods</td>
      <td>module methods, instance methods</td>
    </tr>
    <tr>
      <td><b>inheritance</b></td>
      <td>inherits behaviour and can be base for inheritance</td>
      <td>No inheritance</td>
    </tr>
    <tr>
      <td><b>inclusion</b></td>
      <td>cannot be included</td>
      <td>can be included in classes and modules by using the include command (includes all instance methods as instance methods in class/module)</td>
    </tr>
    <tr>
      <td><b>extension</b></td>
      <td>cannot be extend with extends</td>
      <td>module can extend instance by using extend command (extends given instance with singleton methods from module)</td>
    </tr>
  </tbody>
</table>