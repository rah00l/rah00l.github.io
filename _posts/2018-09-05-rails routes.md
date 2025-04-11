---
layout: post
title: Rails routes
tags: [Ruby, LastMinutePrep]
categories: Rails
style: fill
color: danger
description: In Rails, routers are responsible for mapping incoming requests to controller actions. Here are the main features of Rails routers
---



In Rails, routers are responsible for mapping incoming requests to controller actions. Here are the main features of Rails routers:

1. **RESTful Routing:**

   - Rails encourages RESTful routing, where routes correspond to CRUD (Create, Read, Update, Delete) operations on resources.
   
   - Example:
     ```ruby
     resources :articles
     ```
   - This single line of code creates routes for all CRUD operations on the `articles` resource, such as `GET /articles`, `POST /articles`, `GET /articles/:id`, etc.

2. **Custom Routes:**

   - You can define custom routes for non-CRUD actions or to map URLs to specific controller actions.
   
   - Example:
     ```ruby
     get 'welcome', to: 'pages#home'
     ```
   - This maps the `GET /welcome` request to the `home` action in the `PagesController`.

3. **Route Helpers:**

   - Rails provides route helper methods to generate URLs and paths for routes in views and controllers.
   
   - Example:
     ```ruby
     <%= link_to 'Articles', articles_path %>
     ```
   - This generates a link to the index action of the `ArticlesController`.

4. **Namespaced Routes:**

   - You can organize your routes into namespaces to group related routes and controllers.

   - Example:
     ```ruby
     namespace :admin do
       resources :articles
     end
     ```
   - This creates routes for CRUD operations on articles within the `admin` namespace.

5. **Route Constraints:**

   - You can apply constraints to routes based on request parameters, such as HTTP method, format, or custom conditions.

   - Example:
     ```ruby
     get 'articles/:id', to: 'articles#show', constraints: { id: /\d+/ }
     ```
   - This matches the `GET /articles/:id` route only if `:id` is a digit.

6. **Route Scoping:**

   - You can scope routes to a specific path prefix or namespace, affecting all routes defined within the scope.

   - Example:
     ```ruby
     scope '/admin' do
       resources :articles
     end
     ```
   - This scopes all routes for articles under the `/admin` path.

Rails routers provide a powerful and flexible way to define and manage application routes, making it easier to organize and access different parts of your application.