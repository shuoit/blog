title: 在hexo博客中打造相对完美的URL
entitle: hexo-links
timestamp: 1483800845
date: 2017-01-07 22:54:05
author: 托码思
avatar: /images/favicon.png
authorLink: https://www.tangkunyin.com
authorAbout: https://about.tangkunyin.com
authorDesc: 一个写代码的「伪文人」
categories: 技术
tags: 
	- hexo
	- web
keywords:
photos:
	- /img/2017/hexo-blog-basic.jpg
description: 动态网站的URL可以通过服务器的伪静态规则设置，想怎么搞就怎么搞。可类似hexo这样的静态生成器要想有一个实用好看的URL却不是一件容易的事情，尤其是还带中文的情况。本文将讨论下如何在hexo中打造相对完美的URL
---

![hexo-blog-basic](/img/2017/hexo-blog-basic.jpg)

## 这里讨论一个处女座的情怀问题

如果你是全部用英文写作，那么这篇文章对你确实没什么Luan用。对hexo而言，我们可以很轻松的自定义文章URL。

对数字敏感，你可以在站点的`_config.yml`文件中`permalink`处配置成`:year/:month/:day/:title/`。是的，这个不需要改动，保持hexo默认就是

> 结果是：http://localhost:4000/2017/04/18/demo

有静态网页情结的你，认为`.html`结尾利于`SEO`，则可以把`permalink`配置成`:category/:title.html`或`:category/:timestamp.html`

> 结果是：http://localhost:4000/demo/1492523268.html

注意第二种使用分类别名，请一定要设置两个地方：

```
// 1、站点的_config.yml中找到以下部分
# Category & Tag
default_category: uncategorized
category_map:
    演示: demo
tag_map:

// 2、scaffolds/post.md文件中改成如下
---                                                                                                                               
title: {{ title }}             
date: {{ date }}
tags:                          
timestamp: {{ date }}          
categories: 演示
---
```

是的，增加了两行，在写文章时，通过`hexo n demo`后，生成的`demo.md`文件里如下：

```
---                                                                                                                               
title: hello
categories: 演示
date: 2017-04-18 22:19:45
timestamp: 1447295415
tags:
---
```

此时需要手动的把`timestamp`手动改成时间戳，因为这是你自定义的变量，当前`hexo`还没有这个系统变量使用，所以只能手动配置。

通过以上方式体现的URL更有意义，但是用`title`或`timestamp`面临两个问题，前者遇到中文就呵呵了，后者虽精炼但无疑义。所以还有改进的空间，即使用`permalink: :category/:title.html`这样配置时，创建文章用英文标题。这样解决了中文一坨乱码且意义很明显，读者一看就知道这是哪个分类下的什么文章！

是不是很美好，赶紧试试去吧~



