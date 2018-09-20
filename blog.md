---
layout: page
title: Blog
permalink: /blog/
---
<div class="home">

  {%- if site.posts.size > 0 -%}
      {%- for post in site.posts -%}
      <div id='type-post status-publish format-standard hentry'>
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
          </a>
        </h3>
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        <span class="post-meta">{{ post.date | date: date_format }}</span>
        <div class='post-excerpt'>
        {{ post.excerpt }}
        </div>
        <a class='read-more' href="{{post.url | relative_url }}" title="Read More">
            <span>Read More</span>
        </a>
      </div>
      {%- endfor -%}

    <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>
  {%- endif -%}

</div>
