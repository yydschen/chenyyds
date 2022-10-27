---
title: "给Hugo文章目录添加跟随Scrollspy效果"
date: "2022-10-25"
description: Hugo主题添加动态跟随目录Scrollspy效果
category: 折腾
tags: [hugo,toc,theme,前端]
toc: true
read_more: true
---
![](/images/hugo-toc.png)

## 实现方案

### 目录html的生成
通过Hugo原生的`{{ .TableOfContents }}`函数实现，将下面的代码放入你想将目录放入的部分。
```html
<!-- Put this Hugo function where you want to locate TOC -->
<div class="toc-div">
{{ .TableOfContents }}
</div>
```
<!--more-->
### Gumshoe javascript的插入
添加Gumshoe javascript部分，注意需要将该部分放在整个body末尾。
```html
<script src="<https://cdn.jsdelivr.net/npm/gumshoejs@5.1.2/dist/gumshoe.min.js>"></script>
<script>
	var spy = new Gumshoe('#TableOfContents a', {
	    nested: true,
	    nestedClass: 'active'
    });
</script>
```
### CSS文件的

下面是本站目录部分所采用的CSS样式，可以作为参考。

我用一个流程图描述一下整个`{{ .TableOfContents }}`所生成的html标签的结构

其中高亮所突出部分的样式可在`.active` class下进行相关修改

本站大部分文章正文只会采用两个级别的标题，Hugo默认也只会生成两个级别的目录，如有多级标题的需求，对应修改即可。
```css
/* Both levels of nav */
#TableOfContents li, #TableOfContents ul{
  list-style-type: none;
}

#TableOfContents ul{
  padding-left: 0px;
}

#TableOfContents li > a {
  display: block;
  padding: 4px 20px;
  font-size: 90%;
  color: #919eB1;
}

#TableOfContents li > a:hover,
#TableOfContents li > a:focus {
  padding-left: 19px;
  color: #3A6bA5;
  text-decoration: none;
  background-color: transparent;
  border-left: 1px solid #3A6bA5;
}

#TableOfContents li.active > a,
#TableOfContents li.active > a:hover,
#TableOfContents li.active > a:focus {
  padding-left: 18px;
  font-weight: bold;
  color: #3A6bA5;
  background-color: transparent;
  border-left: 2px solid #3A6bA5;
}

/* Second level */
#TableOfContents li > ul {
  padding-bottom: 10px;
}

#TableOfContents li li > a {
  padding-top: 1px;
  padding-bottom: 1px;
  padding-left: 30px;
  font-size: 12px;
  font-weight: normal;
}

#TableOfContents li li > a:hover,
#TableOfContents li li > a:focus {
  padding-left: 29px;
}

#TableOfContents li li.active > a,
#TableOfContents li li.active > a:hover,
#TableOfContents li li.active > a:focus {
  padding-left: 28px;
  font-weight: 500;
}

#TableOfContents .nav-link.active + ul {
  display: block;
}
```
## 为目录部分加上stick置顶
目录部分与正文部分不同，不应该随页面上下滑动，需要固定显示，一个实现方法是
将目录设置stick的position样式，可以参考下面的css方案。
```css
.toc-div {
    position: -webkit-sticky; /* safari 浏览器 */
    position: sticky; /* 其他浏览器 */
    top: 20px;
}
```
## 折叠隐藏二级标题
如果文章中的标题过多，目录会显得比较臃肿，一个解决方法是，隐藏二级标题，只有浏览部分所在一级标题下的二级标题才展开显示。实现这样的功能也非常简单，只需多添加这样几行样式到css文件中即可。
```css
#TableOfContents li > ul {
    display: none;
}

#TableOfContents li.active > ul {
    display: inherit;
}
```
## 原理
整个动态跟随目录实现的原理非常简单：gumshoe会根据浏览器所在的位置，推算出当前位置在哪个标题下，并将目录部分标题对应的`<li>`标签加上`.active`类，我们在css文件中为`.active`类写上不一样的样式就能实现整个动态跟随高亮的效果。