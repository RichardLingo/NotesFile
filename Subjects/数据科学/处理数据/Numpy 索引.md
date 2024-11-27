---
title: Numpy 索引
authors: Ethan Lin
year: 2024-11-27
tags:
  - 类型/笔记
  - 日期/2024-11-27
  - 内容/处理数据
  - 内容/Numpy
aliases:
  - Numpy 索引
---
# Numpy 索引





```python  
import numpy as np  
  
```  
  
构造一个5×5矩阵，其中，每一个元素值的十位数是行号，个位数是列号  
  
  
```python  
x = np.array([[10 * (i+1) + (j+1) for j in range(5)] for i in range(5)])  
x  
```  
  
  
  
  
```
array([
    [11, 12, 13, 14, 15], 
    [21, 22, 23, 24, 25],  
    [31, 32, 33, 34, 35],  
    [41, 42, 43, 44, 45],  
    [51, 52, 53, 54, 55]
    ])  
```
  
  
  
```python  
ix = np.array([0, 2, 4])  
ixv = np.array([1, 0, 1, 0, 1])  
vx = np.array([True, False, True, False, True])  
iy = np.array([0, 1, 3])  
iyv = np.array([1, 1, 0, 1, 0])  
vy = np.array([True, True, False, True, False])  
```  
  
  
```python  
x[ix, iy]  
```  
  
  
  
```
    array([11, 32, 54])  
```
  
  
```python  
x[ixv, iyv]  
```  
  
  
```
    array([22, 12, 21, 12, 21])  

```
  
  
```python  
x[vx, vy]  
```  
  
  
```
    array([11, 32, 54])  

```
  
```python  
x[np.ix_(ix, iy)]  
```  
  
  
  
```
array([
[11, 12, 14],
[31, 32, 34],
[51, 52, 54]
])  
  
```
  
  
```python  
x[np.ix_(vx,vy)]  
```  
  
  
  
```
    array([
    [11, 12, 14],
	[31, 32, 34],
	[51, 52, 54]
	])  
  
```
  
  
```python  
x[np.ix_(ixv, iyv)]  
```  
  
  
  
```
    array([
    [22, 22, 21, 22, 21],
	[12, 12, 11, 12, 11],
	[22, 22, 21, 22, 21],
	[12, 12, 11, 12, 11],
	[22, 22, 21, 22, 21]
	])  
  
```
  
  
```python  
  
```
