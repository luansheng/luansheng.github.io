# Untitled
Sheng Luan  
2017年6月16日  




```r
library(Rcpp)
setwd("C:/Users/luan_/luansheng/luansheng.github.io/code")
dyn.load("fibonacci.dll")
.Call("fibWrapper",10)
```

```
## [1] 55
```


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

