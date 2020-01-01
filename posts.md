---
layout: default
title: posts
---
<div class="wrapper">
  <div class="home">
    <h2 class="post-list-heading">Posts</h2>
    <ul class="post-list">
    <!-- This loops through the paginated posts -->
    {% for post in paginator.posts %}
      <li>
        <h3><a class="post-link" href="{{ post.url }}">{{ post.title }}</a></h3>
          <span class="post-meta">{{ post.date | date: "%b %-d, %Y"}}</span>
        <p>{{ post.excerpt }}</p>
      </li>
    {% endfor %}
    </ul>
  </div>

  <!-- Pagination links -->
  {% if paginator.total_pages > 1 %}
  <div class="pagination">
    {% if paginator.previous_page %}
      <a href="{{ paginator.previous_page_path | relative_url }}">&laquo; Prev</a>
    {% else %}
      <span>&laquo; Prev</span>
    {% endif %}

    {% for page in (1..paginator.total_pages) %}
      {% if page == paginator.page %}
        <em>{{ page }}</em>
      {% elsif page == 1 %}
        <a href="{{ paginator.previous_page_path | relative_url }}">{{ page }}</a>
      {% else %}
        <a href="{{ site.paginate_path | relative_url | replace: ':num', page }}">{{ page }}</a>
      {% endif %}
    {% endfor %}

    {% if paginator.next_page %}
      <a href="{{ paginator.next_page_path | relative_url }}">Next &raquo;</a>
    {% else %}
      <span>Next &raquo;</span>
    {% endif %}
  </div>
  {% endif %}
</div>