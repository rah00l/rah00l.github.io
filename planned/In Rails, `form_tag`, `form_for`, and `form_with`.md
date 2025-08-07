In Rails, `form_tag`, `form_for`, and `form_with` are used to create HTML forms, but each has its specific use case. Here's a concise explanation of when and why to use each one, along with an example:

1. **form_tag:**
   - `form_tag` is used when you need to create a form that doesn't correspond to a specific model.
   - Example:
     ```ruby
     <%= form_tag '/search', method: 'get' do %>
       <%= text_field_tag :query %>
       <%= submit_tag 'Search' %>
     <% end %>
     ```
   - This creates a form that sends a GET request to `/search`.

2. **form_for:**
   - `form_for` is used when you're working with a specific model and want to create a form for CRUD operations on that model.
   - Example:
     ```ruby
     <%= form_for @article do |f| %>
       <%= f.text_field :title %>
       <%= f.text_area :content %>
       <%= f.submit %>
     <% end %>
     ```
   - This creates a form for creating or updating an `@article` object.

3. **form_with:**
   - `form_with` is a newer form helper introduced in Rails 5.1 that replaces both `form_for` and `form_tag`. It's versatile and can be used for both model and non-model forms.
   - Example for a model form:
     ```ruby
     <%= form_with model: @article do |f| %>
       <%= f.text_field :title %>
       <%= f.text_area :content %>
       <%= f.submit %>
     <% end %>
     ```
   - Example for a non-model form:
     ```ruby
     <%= form_with url: '/search', method: 'get' do %>
       <%= text_field_tag :query %>
       <%= submit_tag 'Search' %>
     <% end %>
     ```
   - `form_with` automatically determines whether to create a model-bound form or a non-model form based on the arguments passed to it.

In summary:
- Use `form_tag` for non-model forms.
- Use `form_for` for model forms.
- Use `form_with` for both model and non-model forms, and it's recommended for newer Rails versions as it offers more flexibility and replaces the other two helpers.

