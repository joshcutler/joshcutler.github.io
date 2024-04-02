---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

My name is Josh.  This is my website.  You can find talks I've given and things I've written here.

## Recent Posts
<ul class="post-list">
{%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
{%- for post in site.posts limit:3 -%}
<li>
    <span class="post-meta">{{ post.date | date: date_format }}</span>
    <h3>
    <a class="post-link" href="{{ post.url | relative_url }}">
        {{ post.title | escape }}
    </a>
    </h3>
    {%- if site.show_excerpts -%}
    {{ post.excerpt }}
    {%- endif -%}
</li>
{%- endfor -%}
</ul>