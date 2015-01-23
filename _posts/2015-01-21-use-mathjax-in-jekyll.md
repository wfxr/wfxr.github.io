---
layout: post
title: 在Jekyll中使用MathJax显示数学公式
category: 技术
tags: markdown tools
description: 如何在Jekyll中添加MathJax支持以显示数学公式
math: true
---

修改html头部，添加如下语句：

{% highlight js %}
<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
{% endhighlight %}

Jekyll中可以将此代码段添加到header.html或default.html中。

## 测试效果

### 质能方程： $$E = mc^2$$

### 矩阵：

<p>
`[(a_11,a_12,a_13),(a_21,a_22,a_23),(a_31,a_32,a_33)]`
</p>

### 二次方程求根：
<p>
When `a != 0`, there are two solutions to `ax^2 + bx + c = 0` and they are
<p style="text-align:center"> 
`x = (-b +- sqrt(b^2-4ac))/(2a) .` </p>
</p>
