---
layout: default
title: Research
---

# Research

Malware analysis, detection engineering, threat hunting, Splunk, Wazuh, and Windows internals.

{% for post in site.posts %}
## [{{ post.title }}]({{ post.url }})
{{ post.excerpt }}
{% endfor %}
