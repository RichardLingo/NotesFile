#知识 
#笔记 
#内容/编程语言/Python
#内容/编程
#内容/科学计算



# Numpy基础

## 数据类型

![[Pasted image 20211101191327.png]]



类型参数及缩写：

类型	字符代码
bool	?, b1
int8	b, i1
uint8	B, u1
int16	h, i2
uint16	H, u2
int32	i, i4
uint32	I, u4
int64	q, i8
uint64	Q, u8
float16	f2, e
float32	f4, f
float64	f8, d
complex64	F4, F
complex128	F8, D
str	a, S（可以在S后面添加数字，表示字符串长度，比如S3表示长度为三的字符串，不写则为最大长度）
unicode	U
object	O
void	V

> 来源：
> [Numpy数据类型对象](https://blog.csdn.net/Zhili_wang/article/details/81140282)



## 常用功能函数


### max、maximum
-   np.max：(a, axis=None, out=None, keepdims=False) 

-   求序列的最值
-   最少接收一个参数
-   axis：默认为列向（也即 axis=0），axis = 1 时为行方向的最值；

-   np.maximum：(X, Y, out=None) 

-   X 与 Y 逐位比较取其大者；
-   最少接收两个参数

来自 <[http://blog.csdn.net/lanchunhui/article/details/52700895](http://blog.csdn.net/lanchunhui/article/details/52700895)>

   

## Numpy多维数组的轴对换 transpose

2018年04月01日 11:49:50 [TzeSing](https://me.csdn.net/weixin_40001181) 阅读数：219

转置中，transpose方法返回的是源数据的视图，就是说修改了视图就会把源数据也改了。

高维数组，transpose的方法如下展示

创建一个形状为(2,2,4)的三维数组：

```python
>>> arr = np.arange(16).reshape((2,2,4))
>>> arr

array([[[ 0, 1, 2, 3],
[ 4, 5, 6, 7]],
[[ 8, 9, 10, 11],
[12, 13, 14, 15]]])
```

transpose需要放入一个由轴编号组成的元组才能变换，意思就是放入需要改变的shape

我需要把 0轴 和 1轴 对调，2轴不变，就是放入(1,0,2)

```python
>>> arr.transpose((1,0,2))

array([[[ 0, 1, 2, 3],
[ 8, 9, 10, 11]],
[[ 4, 5, 6, 7],
[12, 13, 14, 15]]])
```

这里就需要解释一下了，例如第二行的8这个数，这个位置原来是4，4对应的坐标为(0,1,0)——第3维的第一个元素中的第二个元素的第一个。经过transpose转换之后，这个位置对应的便是(1,0,0)中的8，如此类推

## 数组

### 保存数组

   

[Python Numpy数组保存](http://www.cnblogs.com/ice-daigua/archive/2012/11/16/2772674.html)

 Numpy提供了几种数据保存的方法。

 以3\*4数组a为例：

`a.tofile("filename.bin")`

 这种方法只能保存为二进制文件，且不能保存当前数据的行列信息，文件后缀不一定非要是bin，也可以为txt，但不影响保存格式，都是二进制。

 这种保存方法对数据读取有要求，需要手动指定读出来的数据的的dtype，如果指定的格式与保存时的不一致，则读出来的就是错误的数据。

`b = numpy.fromfile("filename.bin",dtype = **)`

 读出来的数据是一维数组，需要利用

 `b.shape = 3,4`重新指定维数。

`numpy.save("filename.npy",a)`

 利用这种方法，保存文件的后缀名字一定会被置为.npy，这种格式最好只用

 `numpy.load("filename")`来读取。

```python
numpy.savetxt("filename.txt",a)
b =  numpy.loadtxt("filename.txt")
```

 用于处理一维和二维数组

来自 <[http://www.cnblogs.com/ice-daigua/archive/2012/11/16/2772674.html](http://www.cnblogs.com/ice-daigua/archive/2012/11/16/2772674.html)>

### 多维数组



### 扩充数组

```python
# 创建一维数组
array_01 = np.array([1, 2, 3, 4, 5, 6])

# 添加一个维度
np.expand_dims(array_01, axis=1)
```

`expand_dims`函数的第一个参数是要添加维度的数组，第二个参数`axis`指定要添加的维度的位置。在这里，我们将`array_01`数组添加了一个维度，使其从一维变为二维，并且按列排列。

此外，也可以用`reshape`。

### 展平数组

`ravel()`

`flatten()`

`reshape()`用元组设置维度

三者介绍与区别：

`reshape`：`reshape`是用来改变数组的形状，但不改变数组的数据。它返回一个新的数组，原数组保持不变。如果新形状无法容纳原数组的所有元素，将会报错。

`flatten`：`flatten`是将多维数组转换成一维数组，返回一个新的数组。它会将原数组的副本展开成一维，不会改变原数组的形状。

`ravel`：`ravel`也是将多维数组转换成一维数组，但它返回的是原数组的视图（view），而不是副本。这意味着对`ravel`返回的数组的修改会影响原数组。`ravel`的性能通常比`flatten`好，因为它返回的是原数组的视图，而不是副本。


### 索引


#### 数组切片

![[Pasted image 20211101195928.png]]