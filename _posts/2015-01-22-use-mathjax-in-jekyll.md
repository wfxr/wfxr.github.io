---
layout: post
title: 在Jekyll中使用MathJax显示数学公式
category: 工具
tags: Jekyll MathJax
description: 如何在Jekyll中添加MathJax支持以显示数学公式
math: true
---

[MathJax](www.mathjax.org)是一个基于Ajax技术的数学表达式显示解决方案（开源）。它能够在HTMl页面中高质量的显示LaTeX和MathML数学符号。支持大部分浏览器，不需要插件，额外字体或安装特殊的阅读器。支持复制/粘贴。

如何利用MathJax在网页中插入公式呢？其实方法很简单， 修改html头部，添加如下语句：

<!-- more -->

{% highlight js %}
<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
{% endhighlight %}

Jekyll中可以将此代码段添加到`header.html`或`default.html`中。

## 测试效果

### 方程： $$E = mc^2$$

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

附上一个在线的MathJax编辑器，可实时预览公式的渲染结果：

[MathJax编辑器](http://lilydjwg.devio.us/misc/mathjax.html#)
