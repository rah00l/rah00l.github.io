---
layout: post
title: AngularJS, Angular 2 and Angular 4
tags: [AngularJS, Angular 2, Angular 4]
categories: Development
---

Differnce between AngularJS, Angular 2 and Angular 4.

Google has released following versions of Angular,

<table width="50%">
  <thead>
    <tr>
    <th>VERSION</th>
      <th>YEAR</th>
    </tr>
  </thead>
  <tbody align="center">
    <tr>
      <td>AngularJS</td>
      <td>2010</td>
    </tr>
    <tr>
      <td>Angular 2</td>
      <td>2016</td>
    </tr>
    <tr>
      <td>Angular 4</td>
      <td>2017</td>
    </tr>
  </tbody>
</table>

Let us disscuss differences between them,

### `AngularJS vs Angular 2`
* Angular 2 is not simple upgrade from Angular 1
* Angular 2 is completely rewritten from the ground up.
* Angular 2 is 5 times faster compared to AngularJS.
* AngularJS was not built for mobile devices, where Angular 2on the other hand is designed from the ground up with mobile support in mind.
* With Angular 2 we have more language choices (JavaScript, TypeScript, Dart, PureScript, Elm, etc)


### `Angular 2 vs Angular 4`
* Changing from Angular 2 to Angular 4 and even future versions of Angular won't be like changing from Angular 1.

* Angular 4 is backwards compatiable with Angular 2 for most applications.

### `What has changed and what is new in Angular 4`
* Some under the hood changes to reduce the size of the compiler generated code.

* TypeScript `2.1` and `2.2` compatibility.

* Animation features are pulledd out of `@angular/core` and are moved into their own package.

* New if/else style syntax with `*ngif` stuctural directive

### `What about Angular 3`

<table>
  <thead>
    <tr>
    <th>LIBRARY</th>
      <th>VERSION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>@angular/core</td>
      <td>v2.3.0</td>
    </tr>
    <tr>
      <td>@angular/compiler</td>
      <td>v2.3.0</td>
    </tr>
    <tr>
      <td>@angular/compiler-cli</td>
      <td>v2.3.0</td>
    </tr>
    <tr>
      <td>@angular/http</td>
      <td>v2.3.0</td>
    </tr>
    <tr style="color: red;">
      <td>@angular/router</td>
      <td>v3.3.0</td>
    </tr>
  </tbody>
</table>

Due to missalignment of the router package's version, the team decided
to go stright for  `Angular 4`.

### `Naming Guidlines`
* Use `AngularJS` to describe versons 1.x or erlier.

* Use `Angular` for versions 2.0.0 and later.

* Use the version numbers `Angular 4.0`, `Angular 2.4` when you need to talk about specific release.
