---
title: 使用pathlib替代os.path
authors: Ethan Lin
year: 2022-12-02 
tags:
  - 日期/2022-12-02 
  - 类型/笔记 
  - 来源/转载 
  - 内容/编程语言/Python 
  - 内容/处理路径 
---


# 使用pathlib替代os.path







`pathlib`可以说是Python 3.6+的替代`os.path`正确之选。


### 基本用法

在过去，文件的路径是纯字符串，现在它会是一个`pathlib.Path`对象:

```python
In : from pathlib import Path

In : p = Path('/home/ubuntu')

In : p
Out: PosixPath('/home/ubuntu')

In : str(p)
Out: '/home/ubuntu'
```

使用str函数可以把一个Path对象转化成字符串。在Python 3.6之前，Path对象是不能作为os模块下的参数的，需要手动转化成字符串:

```python
➜  ~ ipython3.5
Python 3.5.5 (default, Aug  1 2019, 17:00:43)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.3.0 -- An enhanced Interactive Python. Type '?' for help.

In : import os

In : pwd
Out: '/data/home/dongweiming'

In : p = Path('/')

In : os.chdir(p)
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-5-facfc9d7a7d1> in <module>
----> 1 os.chdir(p)

TypeError: argument should be string, bytes or integer, not PosixPath

In : os.chdir(str(p))

In : pwd
Out: '/'
```

从Python 3.6开始，这些接受路径作为参数的函数内部会先通过`os.fspath`调用Path对象的`__fspath__`方法获得字符串类型的路径再去执行下面的逻辑。所以要注意: **如果你想全面使用pathlib模块，应该使用Python3.6或者更高版本！**

### 和os功能对应的方法列表

先看一下os(os.path)模块里部分函数与`pathlib.Path`对应的方法吧。下面列出的这些可以直接用pathlib里面的用法代替:

| os 和 os.path                                                | pathlib                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`os.path.abspath()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.abspath) | [`Path.resolve()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.resolve) |
| [`os.chmod()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.chmod) | [`Path.chmod()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.chmod) |
| [`os.mkdir()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.mkdir) | [`Path.mkdir()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.mkdir) |
| [`os.makedirs()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.makedirs) | [`Path.mkdir()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.mkdir) |
| [`os.rename()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.rename) | [`Path.rename()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.rename) |
| [`os.replace()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.replace) | [`Path.replace()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.replace) |
| [`os.rmdir()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.rmdir) | [`Path.rmdir()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.rmdir) |
| [`os.remove()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.remove), [`os.unlink()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.unlink) | [`Path.unlink()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.unlink) |
| [`os.getcwd()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.getcwd) | [`Path.cwd()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.cwd) |
| [`os.path.exists()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.exists) | [`Path.exists()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.exists) |
| [`os.path.expanduser()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.expanduser) | [`Path.expanduser()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.expanduser) 和 [`Path.home()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.home) |
| [`os.listdir()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.listdir) | [`Path.iterdir()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.iterdir) |
| [`os.path.isdir()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.isdir) | [`Path.is_dir()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.is_dir) |
| [`os.path.isfile()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.isfile) | [`Path.is_file()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.is_file) |
| [`os.path.islink()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.islink) | [`Path.is_symlink()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.is_symlink) |
| [`os.link()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.link) | [`Path.link_to()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.link_to) |
| [`os.symlink()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.symlink) | [`Path.symlink_to()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.symlink_to) |
| [`os.readlink()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.readlink) | [`Path.readlink()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.readlink) |
| [`os.stat()`](https://docs.python.org/zh-cn/3.9/library/os.html#os.stat) | [`Path.stat()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.stat), [`Path.owner()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.owner), [`Path.group()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.group) |
| [`os.path.isabs()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.isabs) | [`PurePath.is_absolute()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.PurePath.is_absolute) |
| [`os.path.join()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.join) | [`PurePath.joinpath()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.PurePath.joinpath) |
| [`os.path.basename()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.basename) | [`PurePath.name`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.PurePath.name) |
| [`os.path.dirname()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.dirname) | [`PurePath.parent`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.PurePath.parent) |
| [`os.path.samefile()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.samefile) | [`Path.samefile()`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.Path.samefile) |
| [`os.path.splitext()`](https://docs.python.org/zh-cn/3.9/library/os.path.html#os.path.splitext) | [`PurePath.suffix`](https://docs.python.org/zh-cn/3.9/library/pathlib.html#pathlib.PurePath.suffix) |



举2个例子:

```python
# 原来的写法
In : os.path.isdir(os.getcwd())
Out: True
# 新的写法
In : Path.cwd().is_dir()
Out: True

# 原来的写法
In : os.path.basename('/usr/local/etc/mongod.conf')
Out: 'mongod.conf'
# 新的写法
In : Path('/usr/local/etc/mongod.conf').name
Out: 'mongod.conf'
```

接着感受下pathlib带来的变化。

### 用`/`拼接路径

过去路径拼接最正确的方法是用`os.path.join`:

```python
In : os.path.join('/', 'home', 'dongwm/code')
Out: '/home/dongwm/code'

In : os.path.join('/home', 'dongwm/code')
Out: '/home/dongwm/code'
```

现在可以用`pathlib.Path`提供的joinpath来拼接:

```python
In : Path('/').joinpath('home', 'dongwm/code')
Out: PosixPath('/home/dongwm/code')
```

但是更简单和方便的方法是用`/`运算符:

```python
In : Path('/') / 'home' / 'dongwm/code'
Out: PosixPath('/home/dongwm/code')

In : Path('/') / Path('home') / 'dongwm/code'
Out: PosixPath('/home/dongwm/code')

In : '/' / Path('home') / 'dongwm/code'
Out: PosixPath('/home/dongwm/code')
```

这也不是什么神奇魔法，有兴趣的可以看Path对象`__truediv__`和`__rtruediv__`方法的实现。

### 链式调用

链式调用是OOP带来的重要改变，从前面的例子中也能感受到，过去的都是把路径作为函数参数传进去，现在可以链式调用。我们看一个更复杂一点的例子：

```python
In : os.path.isfile(os.path.join(os.path.expanduser('~/lyanna'), 'config.py'))
Out: True
```

长不长？现在的写法呢:

```python
In : (Path('~/lyanna').expanduser() / 'config.py').is_file()
Out: True
```

是不是非常符合我们从左向右的阅读习惯呢？

### 自带属性

Path对象带了多个有用的属性

### parent/parents

如果想获得某个文件的父级目录通常需要使用`os.path.dirname`或者字符串的rpartition(或split)方法:

```python
In : p = '/Users/dongweiming/test'

In : p.rpartition('/')[0]
Out: '/Users/dongweiming'

In : p.rsplit('/', maxsplit=1)[0]
Out: '/Users/dongweiming'

In : os.path.dirname(p)
Out: '/Users/dongweiming'
```

如果想获得父级的父级更麻烦一些，例如用`os.path.dirname`，需要这样:

```python
In : from os.path import dirname

In : dirname(dirname(p))
Out: '/Users'
```

使用Path对象的parents属性可以拿到各级目录列表(索引值越大越接近root)，而parent就表示父级目录:

```python
In : p = Path('/Users/dongweiming/test')

In : p.parents[0]
Out: PosixPath('/Users/dongweiming')

In : p.parents[1]
Out: PosixPath('/Users')

In : p.parents[2]
Out: PosixPath('/')

In : p.parent
Out: PosixPath('/Users/dongweiming')

In : p.parent.parent
Out: PosixPath('/Users')
```

由于parent返回的还是Path对象，所以可以链式的获取其parent属性。

### suffix/stem

在过去获得文件后缀名，以及去掉后缀的文件名字，需要使用`os.path.basename`和`os.path.splitext`:

```python
In : base = os.path.basename('/usr/local/etc/my.cnf')

In : base
Out: 'my.cnf'

In : stem, suffix = os.path.splitext(base)

In : stem, suffix
Out: ('my', '.cnf')
```

现在就很方便了:

```python
In : p = Path('/usr/local/etc/my.cnf')

In : p.suffix, p.stem
Out: ('.cnf', 'my')
```

注意: 当文件有多个后缀，可以用`suffixes`返回文件所有后缀列表:

```python
In : Path('my.tar.bz2').suffixes
Out: ['.tar', '.bz2']

In : Path('my.tar').suffixes
Out: ['.tar']

In : Path('my').suffixes
Out: []
```

### 实用方法

Path对象里面有多个实用的方法，我举例一些。

### touch方法

Python语言没有内置创建文件的方法(linux下的touch命令)，过去这么做:

```python
with open('new.txt', 'a') as f:
    ...
```

现在可以直接用Path的touch方法:

```python
Path('new.txt').touch()
```

touch接受`mode`参数，能够在创建时确认文件权限，还能通过`exist_ok`参数方式确认是否可以重复touch(默认可以重复创建，会更新文件的mtime)


### home

获得用户的HOME目录比较常见，过去的写法:

```python
In : os.path.expanduser('~')
Out: '/Users/dongweiming'
```

现在就`Path.home`就可以了:

```python
In : Path.home()
Out: PosixPath('/Users/dongweiming')
```

### 读写文件

Path对象自带了操作文件的方法:

```python
In : p = Path('~/1.txt').expanduser()

In : p.write_text('123\n')
Out: 4

In : p.read_text()
Out: '123\n'

In : p.write_bytes(b'456\n')
Out: 4

In : p.read_bytes()
Out: b'456\n'

In : with p.open() as f:
...:     for line in f:
...:         print(line)
...:
456
```

可以用`write_text`写入字符串，用`write_bytes`将文件以二进制模式打开写入字节数据。对应的，可以用`read_text`读取文本内容，也可以用`read_bytes`以字节对象的形式返回路径指向的文件的二进制内容。还可以用`open`获得文件句柄。

不过需要注意，Path里面带的这几个方法只是一些「快捷方式」，「一次性的」。举个例子:

```python
In : p = Path('~/1.txt').expanduser()

In : p.read_text()
Out: '456\n'

In : p.write_text('789\n')
Out: 4

In : p.write_text('1011\n')
Out: 5

In : p.read_text()
Out: '1011\n'
```

可以看到，多次写入最终结果是最后一次写入的内容。而读取也没有缓存区，全部读取出来。其实读一下源码即能理解:

```python
In [96]: p.read_text??
Signature: p.read_text(encoding=None, errors=None)
Source:
    def read_text(self, encoding=None, errors=None):
        """
        Open the file in text mode, read it, and close the file.
        """
        with self.open(mode='r', encoding=encoding, errors=errors) as f:
            return f.read()
File:      /usr/local/lib/python3.7/pathlib.py
Type:      method

In [97]: p.write_text??
Signature: p.write_text(data, encoding=None, errors=None)
Source:
    def write_text(self, data, encoding=None, errors=None):
        """
        Open the file in text mode, write to it, and close the file.
        """
        if not isinstance(data, str):
            raise TypeError('data must be str, not %s' %
                            data.__class__.__name__)
        with self.open(mode='w', encoding=encoding, errors=errors) as f:
            return f.write(data)
File:      /usr/local/lib/python3.7/pathlib.py
Type:      method
```

**在实际工作中这些方法要谨慎使用!``

### with_name/with_suffix

以前我写过一些修改文件名字或者路径后缀的需求：基于某个文件路径生成另外一个文件路径。举个例子，有一个文件地址`'/home/gentoo/screenshot/abc.jpg'`，2个需求:

1.  获得转成png格式的路径
2.  把图片名字改成`123`

过去需要这么做:

```python
In : p = '/home/gentoo/screenshot/abc.jpg'

In : '{}.png'.format(os.path.splitext(p)[0])
Out: '/home/gentoo/screenshot/abc.png'

In : root, ext = os.path.splitext(p)

In : '{}/{}{}'.format(root.rpartition('/')[0], 123, ext)
Out: '/home/gentoo/screenshot/123.jpg'
```

可读性很差。现在呢:

```python
In : p = Path('/home/gentoo/screenshot/abc.jpg')

In : p.with_suffix('.png')
Out: PosixPath('/home/gentoo/screenshot/abc.png')

In : p.with_name(f'123{p.suffix}')
Out: PosixPath('/home/gentoo/screenshot/123.jpg')
```

### mkdir

过去创建目录时，用`os.mkdir`只能创建一级目录:

```python
In : os.mkdir('1/2/3')
---------------------------------------------------------------------------
FileNotFoundError                         Traceback (most recent call last)
<ipython-input-160-71cc3a9a36b4> in <module>
----> 1 os.mkdir('1/2/3')

FileNotFoundError: [Errno 2] No such file or directory: '1/2/3'
```

所以通常这种一次要创建多级目录，需要用到`os.makedirs`，我一直觉得这么搞很分裂。在Path对应上有mkdir方法，还接受`parents`，以及`mode`、`exist_ok`参数:

```python
In : Path('1/2/3').mkdir()
---------------------------------------------------------------------------
FileNotFoundError                         Traceback (most recent call last)
<ipython-input-163-202ce4b81bf9> in <module>
----> 1 Path('1/2/3').mkdir()

/usr/local/Cellar/python/3.7.3/Frameworks/Python.framework/Versions/3.7/lib/python3.7/pathlib.py in mkdir(self, mode, parents, exist_ok)
   1249             self._raise_closed()
   1250         try:
-> 1251             self._accessor.mkdir(self, mode)
   1252         except FileNotFoundError:
   1253             if not parents or self.parent == self:

FileNotFoundError: [Errno 2] No such file or directory: '1/2/3'

In : Path('1/2/3').mkdir(parents=True)

In : !tree 1/
1/
└── 2
    └── 3

2 directories, 0 files
```

我认为这么用的体验着实好了很多~

### owner

有时候操作文件前需要确认拥有此文件的用户，过去我都是这么写:

```python
In : import pwd

In : pwd.getpwuid(os.stat('/usr/local/etc/my.cnf').st_uid).pw_name
Out: 'dongweiming'
```

现在封装起来可以直接用了:

```python
In : p.owner()
Out: 'dongweiming'
```

### 后记

以上就是我使用的体验了，

> 来源：
> [你应该使用pathlib替代os.path - 知乎](https://zhuanlan.zhihu.com/p/87940289)