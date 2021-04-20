title: 移动端Web适配手记
entitle: 'mobile-web-develop-notes'
author: 托码思
avatar: /images/favicon.png
authorLink: 'https://www.tangkunyin.com'
authorAbout: 'https://about.tangkunyin.com'
authorDesc: 一个写代码的「伪文人」
categories: 技术
timestamp: 1559047955
date: 2019-05-28 20:52:35
tags:
    - 前端
    - web
keywords: rem,vh,vw
description: 移动端下的网页适配笔记。适用于从Native转向H5开发的新手，涉及em、rem、vh、vw百分比这些单位及less插件的应用。
photos:
- /img/2019/15591193255276.jpg
---

### 写在开头

这篇文章所谈到的内容非常基础，如果你已经是一个前端老司机了，请直接掉头加足油门离去。

### 科普

![](/img/2019/15591193255276.jpg)


##### REM

CSS3新增的一个相对单位，来自2013年。全称：`font size of root element`。可根据网页根元素来设置网页中其他字体大小。

其中根元素指**html**标签，即：在`html标签里设置font-size属性，以此来作为其他rem值的基准，从而取得自适配平衡`。另外除了用来设置字体大小，rem还可用来设置视图的：width，height，margin，padding...

##### EM

作用与REM类似，但早于REM，算是他的前辈。全称：`font size of element`。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。其特点如下：

- em值并不是固定的；
- em会继承父级元素的字体大小；

使用rem为元素设定字体大小时，仍然是相对大小，但相对的是HTML根元素。而em是相对于父级元素；


### 与PX的转换

> px像素（Pixel），相对长度单位。是相对于显示器分辨率而言。
 
目前对于大部分浏览器，如果不修改相关字体配置，都是默认显示**font-size:16px**，于是和rem的映射关系有：`16px = 1rem`。


```css
html {
    font-size: 16px;
}
```

如果想给一个p标签设置12px的字体大小，那么用rem表示就是

```css
p {
    font-size: 0.75rem; // 12/16=0.75(rem)
}
```

单位转换的工具：[pxtoem](http://pxtoem.com/)

### 移动端的适配

#### media queries

最早，我刚接触响应式那会儿，是通过**css3的media queries**来获取当前设备屏幕尺寸，然后对应写几套css。但这玩意的副作用就是代码量很大，维护不方便（为了兼顾大屏幕或高清设备，会造成其他设备资源浪费，特别是加载图片。毕竟加载流量和时间也是要成本的）。代表作：**Bootstrap**

#### flex或百分比（%）

号称弹性布局的就是。查得资料所言，大部分套路皆为：flex+百分比的布局方式。另外，不需要适配的地方仍然是px单位。

其中，百分比是相对于父元素，正常情况下是通过属性定义自身或其他元素

#### rem（本文主角）

> 这个特性目前主流浏览器都支持，可以放心用。适配步骤：

##### 1. viewport设置

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
```

其中width=device-width用于设置viewport的宽度等于屏幕的宽度，initial-scale=1.0, maximum-scale=1.0的作用同width=device-width一样

之所以还要同时设置这两者，是因为当它们单独使用时，表现不够完美，即单独使用时，浏览器会有兼容性问题。另外当两者值不同时，浏览器取最大者。

##### 2. 动态设置<html>元素的font-size值。一般有俩姿势：

- 利用css的**media query**来设置，好处是都是在样式表里，好管理；
- 利用**javascript动态操作dom属性来计算基准值**；

样式设置

```css
@media (min-device-width : 375px) and (max-device-width : 667px) and (-webkit-min-device-pixel-ratio : 2){
    html{font-size: 37.5px;}
}
```

脚本设置

```javascript
document.getElementsByTagName('html')[0].style.fontSize = window.innerWidth / 10 + 'px';
```

> 这里除以10，是为了控制数字不至于太大，便于后边计算，是随便定义的。即定义屏宽为：10rem，10rem * font-size = 设计宽度

##### 3. 编写统一的计量单位值

在编写CSS元素时，width/height，margin，padding等时，使用**rem**作为单位。换算方式如上文提到的那样。可以参考以下公式：

**具体属性值** = **设计稿上的标注** / **设计稿的宽度**

当然如果你是使用诸如Less这样的动态样式表语言的话，可以参考下边说的操作。这样就计算出设计稿上的元素和设计稿的比例了。

### 在Less中的应用

为了统一尺寸计算，可以造一个新的单位，比如：wem

```javascript
module.exports = {
    /**
     * 基础尺寸换算。(eg in less: wem(1px))
     * @param less
     * @param pluginManager
     * @param functions
     */
    install(less, pluginManager, functions) {
        functions.add('wem', function(data, base) {
            const bem = base ? base.value : 36;
            return less.dimension(data.value / bem, 'rem');
        });
    },
};
```

在公共的less文件中，可以引用以上插件：

```less
@plugin "less-plugin";

// 外部的使用方式，直接用设计稿尺寸
@contentWidth: wem(360);
@padding: wem(10);
```

此时的360为设计稿的宽度，less插件中的默认36是以设计稿360px为准的，如果你是640px，换成64即可

### 延伸

#### DPR

设备像素比（device pixel radio），设备像素比（dpr） = 设备像素（分辨率）/设备独立像素（屏幕尺寸）。用人话说，就是常说的2倍图、3倍图中的那个数字倍数

一般情况下，我们设置`initial-scale=1.0`就可以了，但是对于dpr大于1的，我们还需要精细化一波操作，可以用**window.devicePixelRatio**获取当前设备的dpr，然后动态设置`viewport`

```javascript
let drp = window.devicePixelRatio;
meta.setAttribute('content', 'initial-scale=' + 1/dpr + ', maximum-scale=' + 1/dpr + ', minimum-scale=' + 1/dpr + ', user-scalable=no');
```

如此，配合rem，就可以完全按设计稿标注来了，也不用再除以2、除以3。好处是：

- 解决了图片高清的问题；
- 解决了border 1px的问题。比如设置了viewport的scale为0.5，在2倍屏下，1px的border就被缩放成0.5px，更为细腻；

#### VW  VH VM

视口单位（Viewport units），之前写Vue时偶然发现的。是相对于视窗的宽高度，值为100。VM（VMin），即：取决于哪个更小

对于移动端而言，最重要的是如何多终端兼容。以上介绍的响应式也好、REM也好都各有千秋，而REM使得CSS和JS耦合在了一起。**VW**和**VH**的诞生解决了这一难题，目前来看，是最趋于完美的做法。具体做法参考以下资料：

### 参考资料： 

- [移动web适配利器-rem](http://www.alloyteam.com/2016/03/mobile-web-adaptation-tool-rem/)
- [掘金：移动端/web端适配方案](https://juejin.im/post/5cbdee71f265da03b57b5866)
- [视区相关单位vw, vh..简介以及可实际应用场景](https://www.zhangxinxu.com/wordpress/2012/09/new-viewport-relative-units-vw-vh-vm-vmin/)
- [vh,vw单位你知道多少？](https://juejin.im/entry/59b00e46f265da2491513bcc)
- [利用视口单位实现适配布局](https://aotu.io/notes/2017/04/28/2017-4-28-CSS-viewport-units/)

#### 在线DEMO

- [REM布局 - 淘宝聚划算](https://jhs.m.taobao.com/m/index.htm#!all)
- [视口单位布局](https://jdc.jd.com/demo/ting/vw_layout.html)

