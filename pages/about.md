---
layout: page
title: About
permalink: /about/
weight: 3
---

# **About Me**
Greetings! My name is **{{ site.author.name }}** :wave: and I am currently residing in Pune, India. With a web development experience that dates back to 2010, my journey started with small Ruby programs and static HTML and eventually, I delved deeper into Ruby on Rails.

I welcome you to my blog, which serves as a platform for me to share my knowledge and insights on software development. Through Short Notes, I aspire to provide a valuable resource to the community by sharing my learnings and experiences.

Thank you for taking the time to read about me, and I look forward to connecting with you through my blog.
<div class="row">
{% include about/skills.html title="Programming Skills" source=site.data.programming-skills %}
{% include about/skills.html title="Other Skills" source=site.data.other-skills %}
</div>

<div class="row">
{% include about/timeline.html %}
</div>