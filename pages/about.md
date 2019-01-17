---
layout: page
title: About
description: Dreams never end.
keywords: Qiaoyi Yin, 殷乔逸
comments: false
menu: 关于
permalink: /about/
---

谁终将声震人间，必长久深自缄默；

谁终将点燃闪电，必长久如云漂泊。

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
