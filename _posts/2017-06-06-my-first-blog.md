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
找了好久，都没有找到如何页内跳转。github page使用如下方法是可行的

点击会跳转,括号里边#后边的"1"表示要跳转的链接的ID
```markdown
[Searle](#1)1980
```
[Searle](#1)1980

定义跳转的目的地，与前边的"1"对应，也称锚点（anchor）
```markdown
<a id="1">1</a> Searle SR, Speed FM, Milliken GA (1980). Population marginal means in the linear model: A alternative to least squares means. The American Statistician, 34(4), 216-221.
```
<a id="1">1</a> Searle SR, Speed FM, Milliken GA (1980). Population marginal means in the linear model: A alternative to least squares means. The American Statistician, 34(4), 216-221.