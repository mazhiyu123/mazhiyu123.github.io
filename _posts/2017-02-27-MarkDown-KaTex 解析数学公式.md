---
published: true
layout: post
title: MarkDown-KaTex 解析数学公式
category: Jekyll
tags: 
  - Jekyll
time: 2017.02.13 11:20:00
excerpt: MarkDown-KaTex 解析数学公式
---


```math

E = mc^2

\frac{1}{x}

\sqrt{e}

a^2

a_1

\sum

```
然而貌似GitHub不支持KaTex解析数学公式

使用forkosh解析数学公式
```
<img src="http://www.forkosh.com/mathtex.cgi? \Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}">
```
效果：
<img src="http://www.forkosh.com/mathtex.cgi? \Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}">


使用google chart解析数学公式
```
<img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}" style="border:none;">
```
效果：
<img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}" style="border:none;">

http://blog.csdn.net/kamidox/article/details/48380239