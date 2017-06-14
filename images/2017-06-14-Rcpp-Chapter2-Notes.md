# Rcpp读书笔记：第二章工具与设置



## 2.1 总体设置

Rcpp提供一个C++应用编程接口（API），来扩展R系统。运行Rcpp和R的一些需求：

* 开发环境需要一个C++编译器。
* 通过动态链接和嵌入方式，供R调用。
* 在windows下，一般会通过安装Rtools来提供开发环境

编译R包所需要的标准环境，通常也是编译Rcpp所需要的。  
几个附加的R包非常有用： 

* inline 对于一些短的C++代码，可以直接通过inline内部编译和调用。
* rbenchmark 用来对比测试不同代码的性能。microbenchmark包也是一个非常好的选择。
* RUnit 用作单元测试。

## 2.2 编译器
windows下，通常使用Rtools自带的g++编译器。

## 2.3 R API
R自身提供了API。API在“Writting R Extensions”手册中进行了详细描述，定义了R安装所需要的头文件。R核心开发团队，建议只使用公开的API。其他API可能会修改或者改变，而不会通知开发者。  

有几本书描述了R API函数。可以参考的包括：  

* Venables WN, Ripley BD (2000) S Programming. Statistics and Computing, pringer-Verlag, New York
* Gentleman R (2009) R Programming for Bioinformatics. Computer Science and
Data Analysis, Chapman & Hall/CRC, Boca Raton, FL
* Chambers JM (2008) Software for Data Analysis: Programming with R. Statistics
and Computing, Springer, Heidelberg, ISBN 978-0-387-75935-7

两个基本的扩展函数：`.C()`和`.Call()`。其中

* `.C()`c存在于R语言的早期版本，并被严格限制使用。仅仅用于支持基本的C类型的指针。
* `.Call()`被广泛应用。该函数主要操作SEXP 对象，代表S expression object的指针。在R内部，本质上任何对象都是SEXP对象。通过在C++和R之间交换对象，实现对R对象的直接操作。Rcpp只使用`.Call()`进行内部调用。

Rcpp本质上是建立在R API基础上的一个补充的接口，可以更好的扩展R。利用C++程序，生成更多可供R调用的工具，来提高R编程的效率。

## 2.4 Rcpp的首次编译


