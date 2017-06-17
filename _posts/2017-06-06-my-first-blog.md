---
layout: post
title: my first blog
categories: jekyll
description: say hello
keywords: github, first blog
---

这是我的第一个blog，架构在github上，基于jekyll。
### 1. 数学公式
关于数学公式的支持问题，对于本博客是在_includes/header.html文件中加入下列语句：
``` javascript   
<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```
github使用的是[kramdown](ttps://help.github.com/articles/updating-your-markdown-processor-to-kramdown/)作为markdown的解析器,而kramdown是支持[公式展示](https://kramdown.gettalong.org/syntax.html#math-blocks)的。具体方法为：

``` Latex
$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$
```
$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$

### 2. 页内跳转
找了好久，都没有找到如何页内跳转。github page使用如下方法是可行的：

"[]"内是点击会跳转的文字,"()"内#后边的"1"表示要跳转的链接的ID。
```markdown
[Searle](#1)(1980)详细的讨论了对于各种因子、嵌套和协方差模型，该如何定义最小二乘均值。
```
[Searle](#1)(1980)详细的讨论了对于各种因子、嵌套和协方差模型，该如何定义最小二乘均值。

定义跳转的目的地，与前边的"1"对应，也称锚点（anchor）
```markdown
<a id="1">1</a> Searle SR, Speed FM, Milliken GA (1980). Population marginal means in the linear model: A alternative to least squares means. The American Statistician, 34(4), 216-221.
```
<a id="1">1</a> Searle SR, Speed FM, Milliken GA (1980). Population marginal means in the linear model: A alternative to least squares means. The American Statistician, 34(4), 216-221.

### 3.关于git的一些小问题（windows系统）
git现在的版本默认使用的是utf-8编码，但是在git log git status时，中文文件名会显示乱码。解决方案：
在git bash中输入下列命令：

```
git config --global core.quotepath false
git config --global gui.encoding utf-8 
git config --global i18n.commitencoding utf-8 
git config --global i18n.logoutputencoding utf-8 
export LESSCHARSET=utf-8
```
管用，但是作用机理不清楚。

git 自带vi，以前用过不断的时间，临时修改几个小地方还是很方便的。如何自动识别中文编码：

```
cd /etc
vi vimrc
```
在打开的vimrc文件开头添加以下代码：
```
set nu
set fencs=utf-8,gbk,utf-16,utf-32,ucs-bom
```
