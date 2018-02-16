---
layout: post
title: Jekyll Tags and Categories on Github Pages
tags: [Jekyll, Github-pages]
categories: Development
---

It is **true** that Github pages do not support customized Ruby plugins for Jekyll site.

We are going to implement Jekyll tagging and categories without plugins, and here is  **step-by-step guide** to achieve so.

### 1. Configure paths at your `_config.yml` file.

{% highlight liquid %}
Tags: '/tags/'
tag_page: '/tags/'

Categories: '/categories/'
category_page: '/categories/'
{% endhighlight %}

### 2. Add tags to your posts

In each of your post file (Markdown, presumably), you need to add the tags to the specific post in the front matter section. For example, for this post, the front matter looks like this:

```
---
layout: post
title: Jekyll Tags and Categories on Github Pages
tags: [jekyll, github-pages]
categories: Web
---
```

### 3. Display the tags of a post

We would like to display the tags of a post inside the post page, like this page. Then the modifications go to the format of posts: `_layouts/post`.

For example, the following code is inserted into the post layout:

{% highlight html %}{% raw %}
<b>Tags:</b> {% if page.tags %} |
{% for tag in page.tags %}
  <a href="{{ site.url }}{{ site.tag_page }}#{{ tag | slugify }}" class="post-tag">{{ tag }}</a>
{% endfor %}
{% endif %}
{% endraw %}{% endhighlight %}

Add css style of `post-tag` class to your custom css file,

{% highlight css %}
.post-tag {
  display: inline-block;
  background: #ac4142;
  padding: 0 .5rem;
  margin-right: .5rem;
  border-radius: 4px;
  color: white !important;
  font-size: 90%;
}
{% endhighlight %}

It will look like:

<img src="{{ site.url }}/public/images/jekyll_tags_on_post.png" />

### 4. Create your categories and tags page

I created `categories.html` and `tags.html` files at root directory. Add following codes to tags.html and categories.html

{% highlight html %} {% raw %}
# tags.html
---
layout: page
title: Tags
---

<div>
  <div>
    {% for tag in site.tags %}
    <a href="#{{ tag[0] | slugify }}" class="post-tag">{{ tag[0] }}</a>
    {% endfor %}
  </div>
  <hr/>
  <div>
    {% for tag in site.tags %}
    <h2 id="{{ tag[0] | slugify }}">{{ tag[0] }}</h2>
    <ul>
      {% for post in tag[1] %}
        <a class="post-title" href="{{ site.baseurl }}{{ post.url }}">
      <li>
        {{ post.title }}
      <small class="post-date">{{ post.date | date_to_string }}</small>
      </li>
      </a>
      {% endfor %}
    </ul>
    {% endfor %}
  </div>
</div>
{% endraw %}{% endhighlight %}

And tags page looks like,

<img src="{{ site.url }}/public/images/tags_page.png" />

{% highlight html %} {% raw %}
# categories.html
---
layout: page
title: Categories
---

<div>
  <div>
    {% for tag in site.categories %}
    <a href="#{{ tag[0] | slugify }}" class="post-tag">{{ tag[0] }}</a>
    {% endfor %}
  </div>
  <hr/>
  <div>
    {% for tag in site.categories %}
    <h2 id="{{ tag[0] | slugify }}">{{ tag[0] }}</h2>
    <ul>
      {% for post in tag[1] %}
        <a class="post-title" href="{{ site.baseurl }}{{ post.url }}">
      <li>
        {{ post.title }}
      <small class="post-date">{{ post.date | date_to_string }}</small>
      </li>
      </a>
      {% endfor %}
    </ul>
    {% endfor %}
  </div>
</div>

{% endraw %}{% endhighlight %}

And categories page looks like,

<img src="{{ site.url }}/public/images/catagories-page.png" />
