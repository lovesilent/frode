---
layout: post
title:	"采用MathJax在Markdown中优雅地书写公式"
data:	2017-12-20
banner_image: psilocybin.jpg
tags: [tech]
---
虽然可以直接使用Markdown来书写公式，但是用起来很繁琐，大多数时候还不如直接链接图片来得爽快。自从发现[Stackoverflow](https://stackoverflow.com)上的公式竟然如此精美，便决定借助[MathJax](https://www.mathjax.org/)来书写公式。

<!--more-->
直接上图，发现右键还有额外功能&#x1F34E;

$$\frac{1}{\Bigl(\sqrt{\phi \sqrt{5}}-\phi\Bigr) e^{\frac25 \pi}} = 1+\frac{e^{-2\pi}} {1+\frac{e^{-4\pi}} {1+\frac{e^{-6\pi}} {1+\frac{e^{-8\pi}} {1+\cdots} } } }$$

本博客是基于Google Pages搭建的，采用了[kramdown](https://kramdown.gettalong.org/math_engine/mathjax.html)解析引擎，默认配置支持MathJax，`$$To Be Write$$`是能够直接被解析为:

{% highlight html %}
<script type="math/tex; mode=display">
	To Be Write
</script>
{% endhighlight %}

因此只需要在html中引入MathJax的CDN就可以使用了。

{% highlight html %}
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

在官方的文档中，还给出了一些MathJax与MarkDown在语法解析上存在分歧的相应解决方案（配置额外的scripts），这里就不详细列出了。

总之，运用了MathJax后，公式变得很美，看起来让人舒心！

{% endhighlight %}


