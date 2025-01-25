---
title:
  - Python和Anaconda切换镜像源
authors: Ethan Lin
year: 2024-03-19
tags:
  - 类型/笔记
  - 日期/2024-03-19
  - 来源/转载
  - 内容/软件/Anaconda
  - 内容/编程语言/Python
---



[[配置Python]]
[[配置 Anaconda]]

# Python 和 Anaconda切换镜像源

Python默认的镜像源是（逐一尝试）：
```bash
https://pypi.python.org/
https://pypi.org/simple
```

若要把 pip 源换成国内的，只需要把上面的代码改成下图这样（下图以清华大学源为例）：

```bash
pip install <your-pip-package> -i https://pypi.tuna.tsinghua.edu.cn/simple
```


上述做法是临时改成国内源，如果不想每次用 pip 都加上
```bash
-i https://pypi.tuna.tsinghua.edu.cn/simple
```
，那么可以把国内源设为默认，做法是：

清华源：
```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

阿里源：
```bash
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
```

腾讯源：
```bash
pip config set global.index-url http://mirrors.cloud.tencent.com/pypi/simple
```

豆瓣源：
```bash
pip config set global.index-url http://pypi.douban.com/simple/
```

中国科学技术大学源：
```bash
pip config set global.index-url http://pypi.mirrors.ustc.edu.cn/simple/
```



Anaconda 永久切换镜像源：

```shell
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/linux-64/
conda config --set show_channel_urls yes
```
