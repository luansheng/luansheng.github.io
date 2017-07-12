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

## 2.5 inline
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

函数中添加**`verbose=TRUE`**参数可以显示`cxxfunction()`生成的临时文件和R CMD SHLIB触发的调用。

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
##   23 : SEXP file9eec3f1e11be( SEXP a, SEXP b) ;
##   24 : }
##   25 : 
##   26 : // definition
##   27 : 
##   28 : SEXP file9eec3f1e11be( SEXP a, SEXP b ){
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
##  C:/PROGRA~1/R/R-34~1.0PA/bin/x64/R CMD SHLIB file9eec3f1e11be.cpp 2> file9eec3f1e11be.cpp.err.txt
```
从显示的文件中，可以看出，`cxxfunciton()`函数实际上在内部生成一个随机函数，并且进行了声明和定义。
声明部分,其中对a和b两个变量定义为SEXP类型。
```cpp
// declarations                          
extern "C" {                             
SEXP file9864474b192a( SEXP a, SEXP b) ; 
}                                        
```
定义部分，`BEGIN_RCPP`和`END_RCPP`两个宏的作用，还不知道。书中说后续会解释。
```cpp
// definition                               
                                            
SEXP file9864474b192a( SEXP a, SEXP b ){    
BEGIN_RCPP                                  
                                            
Rcpp::NumericVector xa(a);                  
Rcpp::NumericVector xb(b);                  
int n_xa =xa.size(), n_xb = xb.size();      
                                            
Rcpp::NumericVector xab(n_xa + n_xb-1);     
for (int i=0; i < n_xa; i++)                
  for (int j=0; j < n_xb; j++)              
    xab[i+j] += xa[i] * xb[j];              
return xab;                                 
                                            
END_RCPP                                    
}                                           
```
