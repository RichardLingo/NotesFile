---
title: R语言获取list指定下标
authors: Ethan Lin
year:
tags:
  - 内容/编程/R语言 
---

# R语言获取list指定下标






`which(x==a)`：返回一个包含x符合条件(当比较运算结果为真(TRUE)的下标的向 量，在这个结果向量中数值i说明x[i] == a(这个函数的参数必须是 逻辑型变量)
例子：
```r
> f=c("b","c","a")
> f
[1] "b" "c" "a"
> which(f=="c")
[1] 2
> f[which(f=="c")]
[1] "c"
```