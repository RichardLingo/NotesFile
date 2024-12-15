---
title: R与Stata在计量经济学中的比较
authors: Ethan Lin
year: 2024-12-15
tags:
  - 类型/笔记
  - 日期/2024-12-15
  - 来源/转载
  - 内容/R语言
  - 内容/Stata
  - 类型/辨析
aliases:
  - R与Stata在计量经济学中的比较
---
# R与Stata在计量经济学中的比较



> **作者**：陈卓然(中山大学)  
> **邮箱**：[chenzhr25@mail2.sysu.edu.cn](mailto:chenzhr25@mail2.sysu.edu.cn)

> **编者按**：本文主要参考自 Paul Goldsmith-Pinkham 教授的博客「[Comparing tidyverse R to Stata](https://paulgp.github.io/blog.html)」，特此致谢！

---

**目录**  
[[TOC]]

---

基础的 R 语言在计量分析当中往往十分笨拙，但是在一些外部包中，这一问题已经部分得到了解决，例如 `tidyverse` 包。本推文将对 R 和 Stata 进行比较，探究二者在计量分析中各自存在的优劣势。

## 1. 数据集简介

本推文使用的数据集为 nlswork.dta，它是对美国在 1968 年时 14 到 24 岁年轻女性的 21 年调查数据。该数据集共有 4711 位女性，在这段时间内她们都处于工作状态，并且小时工资在 1-700 美元之间。我们研究的是在所有已婚人士中，相比于其他人种，白种人是否会更加努力勤奋工作。

## 2. 数据清洗与估计

首先用 Stata 进行估计，具体命令如下：

```stata
. * load Data + create variables
. lxhuse nlswork, clear
. keep if msp == 1 // 只保留已婚人士
. qui sum wks_work
. gen HardWorker = wks_work > r(mean) if ~missing(wks_work) 
. //wks_work指去年总共工作的周数，生成勤奋工作者的虚拟变量
. gen white = race == 1 if ~missing(race) // 生成白种人
. keep HardWorker white idcode year grade

. * Basic summary stats by groups
. mean white
. tab year, sum(white)             // 按照年份进行分组统计
. tab idcode in 1/100, sum(white)  // 按照个体进行分组统计

. * Simple Panel Regression
. areg HardWorker    white, absorb(year) cluster(idcode)
. * 或者采用下述命令
. reghdfe HardWorker white, absorb(year) cluster(idcode)  

HDFE Linear regression                            Number of obs   =     16,763
Absorbing 1 HDFE group                            F(   1,   3612) =       2.69
Statistics robust to heteroskedasticity           Prob > F        =     0.1012
                                                  R-squared       =     0.5468
                                                  Adj R-squared   =     0.5464
                                                  Within R-sq.    =     0.0002
Number of clusters (idcode)  =      3,613         Root MSE        =     0.3272
                             (Std. err. adjusted for 3,613 clusters in idcode)
------------------------------------------------------------------------------
             |               Robust
  HardWorker | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
-------------+----------------------------------------------------------------
       white |     -0.011      0.007    -1.64   0.101       -0.024       0.002
       _cons |      0.390      0.006    66.59   0.000        0.379       0.402
------------------------------------------------------------------------------
```

接下来我们使用 R 进行同样的操作。在展示最终的结果之前，有必要了解一下在 R 中如何进行高维固定效应的估计。我们使用的命令是 `felm`, 这一命令是内嵌在 `lfe` 包中的，因此在使用之前需要先加载 `lfe`，即 `library(lfe)`。这一命令的语法格式是：

```R
felm(
  formula,
  data,
  exactDOF = FALSE,
  subset,
  na.action,
  contrasts = NULL,
  weights = NULL,
  ...
)
```

这里主要介绍 `formula` 部分，其余部分使用不多，此处暂且略过。`formula` 这一部分是由四个子部分构成的，第一部分是普通的协变量，也就是控制变量；第二部分是需要控制的固定效应；第三部分是 IV 的设定；第四部分是标准误的聚类设定。也就是类似如下形式：

```R
y ~ x1 + x2 | f1 + f2 | (Q|W ~ x3 + x4) | clu1 + clu2
```

其中，`y` 是被解释变量，`x1` 和 `x2` 是普通的协变量，`f1` 和 `f2` 是需要控制的固定效应，`Q`和 `W` 是内生变量，它们的工具变量是 `x3`和 `x4`，`clu1` 和 `clu2` 是用来计算聚类标准误的变量。上述四个部分中，如果某一部分不存在，就用 0 代替，除非是最后一部分 (0 可以省略)。下面是使用 R 进行上述相同操作的代码：

```R
library("readxl")
library("dplyr")
library("tidyr")
library("broom")
library("lfe")
nlswork <- read_excel(path = "D:\\Stata17\\personal\\连享会推文\\推文blog\\推文6-reghdfe\\nlswork.xlsx")
## 保留已婚人士
## 生成勤奋工作者
## 生成白种人

reghdfeData <- nlswork %>% filter(msp == 1) %>%
  mutate(Hardworkers = wks_work > mean(wks_work, na.rm = TRUE),
         White = race == "White") %>%
  select(idcode, year, Hardworkers, White)
summary(reghdfeData)

## Basic Summary Statistics (仅能给出均值，但是在Stata中可以给出
## 标准误以及置信区间)
reghdfeData %>% summarise(mean(White)) 
## 分组回归的结果 (Stata 中不能将mean 与 bys连用，因此只能使用sum)
### 按年份分组
reghdfeData %>% group_by(year) %>% summarise(mean(White))
### 按个体分组
reghdfeData %>% group_by(idcode) %>% summarise(mean(White))
## 回归：探究是否为白人对是否努力工作的影响 (高维固定效应，类似于
## Stata中的reghdfe)
est <- felm(Hardworkers ~ White | year | 0 | idcode,data = reghdfeData)
tidy(est)
summary(est)
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E9%99%88%E5%8D%93%E7%84%B6-RStataCompare-felmR-Results.jpg)

这是一个非常简单的小例子，我们并不需要做很多数据清理的工作，看上去 Stata 和 R 并没有显著区别。但是当我们需要做很多数据清洗工作时，尤其是涉及到合并数据集，我们最好还是选择 R。R 中可以创建不同的矩阵或者向量存储数据，因而可以很方便的纵向合并或者横向合并数据。在 Stata 当中，当涉及到合并数据的操作时，我们不得不反复加载和存储数据到内存中，显得很麻烦。

从结果来看，尽管二者完全一样，但是很明显，Stata 中的结果输出地更加友好，相比之下，R 输出的结果略显凌乱，因此在结果输出方面，Stata 要略胜一筹。

## 3. 加入工具变量

我们不妨假设教育背景与是否勤奋工作无关，而与是否为白种人相关。白种人往往可以获得比其他人种更多的学习机会，因而拥有更高的教育背景，而是否勤奋工作与教育背景不相关。注意此处仅作为演示，上述工具变量相关性和外生性的论述可能不太恰当。具体地，使用 Stata 估计的命令如下：

```stata
. * 加入工具变量
. ivreghdfe HardWorker (white = grade), absorb(year) cluster(idcode)

IV (2SLS) estimation
------------------------------------------------------------------------------
             |               Robust
  HardWorker | Coefficient  std. err.      t    P>|t|     [95% conf. interval]
-------------+----------------------------------------------------------------
       white |      0.348      0.077     4.50   0.000        0.196       0.499
------------------------------------------------------------------------------
Underidentification test (Kleibergen-Paap rk LM statistic):             46.819
                                                   Chi-sq(1) P-val =    0.0000
------------------------------------------------------------------------------
Weak identification test (Cragg-Donald Wald F statistic):              336.528
                         (Kleibergen-Paap rk Wald F statistic):         52.745
Stock-Yogo weak ID test critical values: 10% maximal IV size             16.38
                                         15% maximal IV size              8.96
                                         20% maximal IV size              6.66
                                         25% maximal IV size              5.53
Source: Stock-Yogo (2005).  Reproduced by permission.
NB: Critical values are for Cragg-Donald F statistic and i.i.d. errors.
------------------------------------------------------------------------------
Hansen J statistic (overidentification test of all instruments):         0.000
                                                 (equation exactly identified)
------------------------------------------------------------------------------
Instrumented:         white
Excluded instruments: grade
Partialled-out:       _cons
                      nb: total SS, model F and R2s are after partialling-out;
                          any small-sample adjustments include partialled-out
                          variables in regressor count K
------------------------------------------------------------------------------
```

使用 R 估计的命令如下：

```R
## 加入工具变量之后进行回归
est2 <- felm(Hardworkers ~ 0 | year | (White ~ grade) | idcode, data = reghdfeData)
tidy(est2)
summary(est2)
```

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/%E9%99%88%E5%8D%93%E7%84%B6-RStataCompare-felmR-Results-IV.jpg)

## 4. 其他方面的比较

下面我将简要介绍一些常用的 R 和 Stata 处理相同任务时的命令，以方便小伙伴们在两种语言之间快速切换。

|Stata|R|Description|
|---|---|---|
|`clear all`|`rm(list = ls())`|Clears data, value labels, etc from memory|
|`insheet using "foo.csv", comma names`|`mydata <- read.csv("foo.csv", header = TRUE)`|Change working directories|
|`pwd`|`getwd()`|Display the working directory|
|`reg y x1 x2`|`summary(lm(y ~ x1 + x2, data = mydata))`|Ordinary least squares with constant|
|`reg y x1 x2, nocon`|`summary(lm(y~x1+x2-1, data = mydata))`|Ordinary least squares without constant|
|`reg y x if x>0`|`lm(y~x,data = subset(mydata, x>0))`|Select a conditional subset of data|
|`forvalues i = 1/100 {...}`|`for (i in 1:100) {...}`|Loop through integer values of i from 1 to 100|
|`foreach i of varlist "a b c" {...}`|`for (i in c("a","b","c")) {...}`|Loop through a list of items|
|`logit y x`|`summary(glm(y~x,data = mydata, family = "binomial"))`|Perform logit maximum likelihood estimatio|
|`probit y x`|`summary(glm(y~x, data = mydata, family = binomial(link = "probit")))`|Perform probit maximum likelihood estimation|
|`sort x y`|`mydata[order(mydata\$x, mydata\$y), ]`|Sort the data frame by variable x|
|`summarize`|`summary(mydata)`|Provide summary values for data|
|`replace x = y1 + y2`|`mydata\$x<-with(mydata, y1+y2)`|Change the x value of data to be equal to y1+y2|
|`drop x`|`mydata\$x<-NULL`|Drop variable x from the data|
|`append using "mydata2.dta"`|`mydata<-rbind(mydata,mydata2)`|Append mydata2 to mydata|
|`merge 1:1 index using "mydata2.dta"`|`merge(mydata, mydata2, index)`|Merge two data sets together by index variable(s)|
|`set obs 1000 /// gen x = rnormal()`|`mydata\$x<-rnorm(1000)`|Generate 1000 random normal draws|
|`count`|`nrow(mydata)`|Count the number of observations in the data|
|`rename oldvar newvar`|`colnames(dataframe)[colnames(dataframe) == "oldvar"]<-"newvar"`|Rename Variables|
|`egen id = group(x y)`|`mydata\$ID <- with(mydata, ave(ID, list(x,y), FUN = seq_along))`|Create an identifier ID from variables x and y|
|`egen smallist = min(x y)`|`mydata %>% rowwise() %>% mutate(smallest = min(c(x,y)))`|For each row, find the smallest value within a set of columns|
|`egne newvar = fcn(x y)`|`mydata %>% rowwise() %>% mutate(newvar = fcn(c(x,y)))`|For each row, apply a function to variables|

## 5. 参考文献

- Dictionary: Stata to R [-Link-](https://github.com/EconometricsBySimulation/RStata/wiki/Dictionary%3A-Stata-to-R)
- Comparing tidyverse R to Stata [-Link-](https://paulgp.github.io/blog.html)
- Muenchen R A, Hilbe J. R for Stata users[M]. New York, NY: Springer, 2010. [-PDF-](http://www.allahbaksh.com/training/Books/R_programming/R%20for%20Stata%20Users.pdf)
- Stata & R Command Dictionary. [-PDF-](http://www.torsten-heinrich.com/blog/R-Stata.pdf)
- Getting Started in R-Stata Notes on Exploring Data. [-PDF-](http://www.princeton.edu/~otorres/RStata.pdf)