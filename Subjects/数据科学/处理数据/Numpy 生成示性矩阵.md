---
title: Numpy 生成示性矩阵
authors: Ethan Lin
year: 2025-01-05
tags:
  - 类型/笔记
  - 日期/2025-01-05
  - "#内容/Numpy"
  - 内容/处理数据
aliases:
  - Numpy 生成示性矩阵
---
# Numpy 生成示性矩阵




```python
import numpy as np
rand8=np.random.choice(3,10)
print(rand8)
np.arange(3)==rand8[:,np.newaxis]
```

结果：

```text
array([2, 1, 1, 0, 1, 1, 2, 1, 0, 1])


array([[False, False,  True],
       [False,  True, False],
       [False,  True, False],
       [ True, False, False],
       [False,  True, False],
       [False,  True, False],
       [False, False,  True],
       [False,  True, False],
       [ True, False, False],
       [False,  True, False]])
```