title: Vue学习笔记
entitle: 'vue-study-notes'
author: 托码思
avatar: /images/favicon.png
authorLink: 'https://www.tangkunyin.com'
authorAbout: 'https://about.tangkunyin.com'
authorDesc: 一个写代码的「伪文人」
categories: 技术
timestamp: 1528027974
date: 2018-06-03 20:12:54
tags:
    - vue
    - javascript
keywords: Vue
description: 来自2018.5.12的笔记：Vue学习手记。高手请有默契的绕道...
photos:
    - /img/2018/15280280857568.jpg
---

### 一，建立一个Vue工程：

![](/img/2018/15280280857568.jpg)

然后就完了....

### 二，打开这个功能，命令行执行：npm run dev ，就可以看到正常的网页

### 三，单文件组件的写法与用法：

1. 新建一个vue文件，写好内容；
2. 需要的地方引入，其中注意 @ 为 Vue自带的，表示src。如以下引用方式：
![](/img/2018/15280281314771.jpg)

3. 使用Less支持，package.json中添加：less和less-loader，然后文件夹：build->webpack-base-config.js中添加如下：
```javascript
{
  test: /\.less$/,
  loader: 'style-loader!css-loader!less-loader'
}
```

![](/img/2018/15280281796136.jpg)


## 暂时玩儿到这里，有需要会继续折腾...

代码： [Hello-FE](https://github.com/tangkunyin/Hello-FE)

