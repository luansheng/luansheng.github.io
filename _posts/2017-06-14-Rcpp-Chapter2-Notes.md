---
layout: post
title: Rcpp读书笔记：第二章工具与设置
categories: Rcpp R C++
description: 详细解释Rcpp的编译过程
keywords: Rcpp, R, 编译, API
---
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
将下列C++代码存为fibonacii.cpp文件。
```cpp
#include <Rcpp.h>
int fibonacci(const int x) {
  if (x == 0) return(0);
  if (x == 1) return(1);
  return (fibonacci(x - 1)) + fibonacci(x - 2);
}

extern "C" SEXP fibWrapper(SEXP xs) {
  int x = Rcpp::as<int>(xs);
  int fib = fibonacci(x);
  return (Rcpp::wrap(fib));
}
```
如何编译上述代码？需要进行以下几项准备工作（本文主要针对windows 10系统下）：

* 首先R CMD命令要可以运行。安装最新的[Rtools](https://cran.r-project.org/bin/windows/Rtools/)。安装完成后，设置环境变量，在path中添加R主程序所在的路径，本文中用的是R3.4可执行文件所在的路径，本文所用路径为：`C:\Program Files\R\R-3.4.0patched\bin`。添加完成后，Win+R，输入cmd，然后在命令行中输入path，查看是否添加成功。
* 查找Rcpp包所在的路径，本文Rcpp包所在路径为`C:/Users/luan_/Documents/R/win-library/3.4/Rcpp/`。
* 注意windows输出的路径是"\\"需要替换为"/"。

完成上述两项工作后，win+R，输入cmd，通过cd命令切换到fibonacci.cpp文件所在的路径，在窗口中输入下边的命令：
```
PKG_CXXFLAGS="-I/C:/Users/luan_/Documents/R/win-library/3.4/Rcpp/include" \
PKG_LIBS="-L/C:/Users/luan_/Documents/R/win-library/3.4/Rcpp/libs/x64 -lRcpp" \
R CMD SHLIB fibonacci.cpp
```
* 命令第一行表示头文件所在的路径；  
* 命令第二行表示库文件所在的路径和名称；
* 命令第三行表示编译和链接代码。

R CMD SHLIB命令执行后，会输出如下信息：
```
c:/Rtools/mingw_64/bin/g++  -I"C:/PROGRA~1/R/R-34~1.0PA/include" -DNDEBUG 
                            -I "d:/Compiler/gcc-4.9.3/local330/include"  
                            -IC:/Users/luan_/Documents/R/win-library/3.4/Rcpp/include
                            -O2 -Wall  -mtune=core2 -c fibonacci.cpp -o fibonacci.o
c:/Rtools/mingw_64/bin/g++ -shared -s -static-libgcc -o fibonacci.dll tmp.def fibonacci.o 
                            -LC:/Users/luan_/Documents/R/win-library/3.4/Rcpp/libs/x64
                            -lRcpp -Ld:/Compiler/gcc-4.9.3/local330/lib/x64 
                            -Ld:/Compiler/gcc-4.9.3/local330/lib 
                            -LC:/PROGRA~1/R/R-34~1.0PA/bin/x64 -lR
```
命令主要触发两个调用：

* 第一条命令将源文件fibonacci.cpp转换为目标文件fibonacci.o;
* 第二条命令将目标文件fibonacci.io链接为一个共享库文件fibonacci.dll。

接下来的工作是调用生成的共享链接库fibonacci.dll文件。
在R中加载Rcpp库，通过`setwd()`切换到fibonacci.dll所在路径,通过`dyn.load()`和`.Call()`进行加载和调用。
```r
library(Rcpp)
setwd("C:/Users/luan_/luansheng/luansheng.github.io/code")
dyn.load("fibonacci.dll")
.Call("fibWrapper",10)
```
输出结果为：
```
## [1] 55
```

Rcpp提供了2个script，来对最原始的代码进行辅助编译。
这里需要注意，由于Rcpp是通过Rstudio进行安装的，因此Rcpp并不在R的标准库路径下。  

```r
.Library
```

```
## [1] "C:/PROGRA~1/R/R-34~1.0PA/library"
```

```r
.libPaths()
```

```
## [1] "C:/Users/luan_/Documents/R/win-library/3.4"
## [2] "C:/Program Files/R/R-3.4.0patched/library"
```
直接运行下边的代码，显示找不到Rcpp，但是又找不到方法，把缺省的库设置为`C:/Users/luan_/Documents/R/win-library/3.4`
```
PKG_CXXFLAGS=`Rscript -e 'Rcpp:::CxxFlags()'` \
PKG_LIBS=`Rscript -e 'Rcpp:::LdFlags()'` \
R CMD SHLIB fibonacci.cpp
```
## 2.5 inline 包
现在再理解起来，比第一章要容易的多了。inline包适合快速开发，因为C++代码可以直接在R代码中书写，直接与R代码一起运行，C++代码的编译、链接和载入在后台进行，特别方便。

inline提供函数`cfunction()`和`cxxfunction()`，在R会话中直接编译、链接和加载C++函数。
在`cxxfunction()`函数中,可以设置plugin参数，用来指定头文件和库位置，在本文中，一般会指定为Rcpp。因此，为了简化使用，inline提供一个cxx`function()`的替代函数`rcpp()`,默认plugin为rcpp。

来看一个示例代码，卷积的计算公式。
卷积好像是一种预测，对不停的信号输入，可以预测其输出。知乎上有个以复利进行解释的的例子，比较容易的理解。https://www.zhihu.com/question/22298352

```r
##定义C++代码
src <- '
Rcpp::NumericVector xa(a);
Rcpp::NumericVector xb(b);
int n_xa =xa.size(), n_xb = xb.size();

Rcpp::NumericVector xab(n_xa + n_xb-1);
for (int i=0; i < n_xa; i++)
  for (int j=0; j < n_xb; j++)
    xab[i+j] += xa[i] * xb[j];
return xab;
'

###定义在R中调用的接口函数
fun <- cxxfunction(signature(a="numeric",b="numeric"),
src,plugin="Rcpp")

#利用rcpp函数定义接口函数
funrcpp <-  rcpp(signature(a="numerci",b="numeric"),src)

##记得加载Rcpp和inline两个包，不然会找不到Rcpp调用的类型和函数
require(Rcpp)
require(inline)
fun(1:4,2:5)
```

```
## [1]  2  7 16 30 34 31 20
```

```r
funrcpp(1:4,2:5)
```

```
## [1]  2  7 16 30 34 31 20
```

函数中添加`verbose=TRUE`参数可以显示`cxxfunction()`生成的临时文件和R CMD SHLIB触发的调用。

```r
funverbose <- cxxfunction(signature(a="numeric",b="numeric"),
src,plugin="Rcpp",verbose = TRUE)
```

```
##  >> setting environment variables: 
## PKG_LIBS = 
## 
##  >> LinkingTo : Rcpp
## CLINK_CPPFLAGS =  -I"C:/Users/luan_/Documents/R/win-library/3.4/Rcpp/include" 
## 
##  >> Program source :
## 
##    1 : 
##    2 : // includes from the plugin
##    3 : 
##    4 : #include <Rcpp.h>
##    5 : 
##    6 : 
##    7 : #ifndef BEGIN_RCPP
##    8 : #define BEGIN_RCPP
##    9 : #endif
##   10 : 
##   11 : #ifndef END_RCPP
##   12 : #define END_RCPP
##   13 : #endif
##   14 : 
##   15 : using namespace Rcpp;
##   16 : 
##   17 : 
##   18 : // user includes
##   19 : 
##   20 : 
##   21 : // declarations
##   22 : extern "C" {
##   23 : SEXP file9864474b192a( SEXP a, SEXP b) ;
##   24 : }
##   25 : 
##   26 : // definition
##   27 : 
##   28 : SEXP file9864474b192a( SEXP a, SEXP b ){
##   29 : BEGIN_RCPP
##   30 : 
##   31 : Rcpp::NumericVector xa(a);
##   32 : Rcpp::NumericVector xb(b);
##   33 : int n_xa =xa.size(), n_xb = xb.size();
##   34 : 
##   35 : Rcpp::NumericVector xab(n_xa + n_xb-1);
##   36 : for (int i=0; i < n_xa; i++)
##   37 :   for (int j=0; j < n_xb; j++)
##   38 :     xab[i+j] += xa[i] * xb[j];
##   39 : return xab;
##   40 : 
##   41 : END_RCPP
##   42 : }
##   43 : 
##   44 : 
## Compilation argument:
##  C:/PROGRA~1/R/R-34~1.0PA/bin/x64/R CMD SHLIB file9864474b192a.cpp 2> file9864474b192a.cpp.err.txt
```