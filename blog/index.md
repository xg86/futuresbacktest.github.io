---
title: Blog
permalink: /blog/
layout: posts
---

# FuturesBacktest Blog

{: .post-excerpt}
FuturesBacktest is a web-based platform to backtest futures contracts portfolios without writing a single line of code. Different flavours of trend, carry and value strategies over 50+ futures contracts, as well as mean-variance, risk parity or risk budgeting allocations are available out of the box. Read  the latest articles from our blog!

{% for post in site.posts %}
{::options parse_block_html="true" /}
<div>
## [{{ post.title }}]({{ post.url }})
{% if post.last_modified_at %}
{{post.date | date: '%B %-d, %Y' }} (updated on {{post.last_modified_at | date: '%B %-d, %Y' }}), by {{post.author}}
{% else %}
{{post.date | date: '%B %-d, %Y' }}, by {{post.author}}
{% endif %}

{: .post-excerpt}
{{post.excerpt}}

[READ MORE &raquo;]({{ post.url }})
</div>

{% endfor %}

<div markdown="0" class="subscribe">
    <p>Create a free account and start backtesting now!</p>
    <a role="button" class="btn btn-primary" href="/signup/">Sign up for a free account</a>
</div>