---
title: NumPy二维数组通过二维索引之后结果保持二维形状
authors: Ethan Lin
year: 2023-04-12
tags:
  - 类型/笔记
  - 日期/2023-04-12
  - 内容/Numpy
  - 内容/处理数据
aliases:
  - NumPy二维数组通过二维索引之后结果保持二维形状
---
# NumPy二维数组通过二维索引之后结果保持二维形状


在 NumPy 中，可以使用二维布尔数组作为索引来选择二维数组中的元素。这里的布尔数组必须具有与原始数组相同的形状。

要让 `selected_arr` 返回的结果保持原来的二维形状，可以使用布尔索引的高级用法，即使用布尔数组作为索引选择行或列。具体地，通过在布尔数组中使用 `np.any` 或 `np.all` 函数，可以选择任何满足特定条件的行或列。

例如，假设我们有一个形状为 `(3, 4)` 的二维数组 `arr`，并且我们想要选择其中所有大于 5 的元素，并将结果存储在一个形状相同的数组 `selected_arr` 中。我们可以使用以下代码：

```
import numpy as np

arr = np.array([[1, 2, 3, 4],
                [5, 6, 7, 8],
                [9, 10, 11, 12]])

bool_arr = arr > 5
selected_arr = np.zeros_like(arr)
selected_arr[bool_arr] = arr[bool_arr]

selected_rows = np.any(bool_arr, axis=1)
selected_arr = selected_arr[selected_rows, :]
```

在这里，我们首先使用布尔数组 `bool_arr` 来选择所有大于 5 的元素，然后将这些元素存储在 `selected_arr` 中。然后，我们使用 `np.any(bool_arr, axis=1)` 来选择所有至少包含一个选定元素的行，并将其存储在 `selected_rows` 中。最后，我们使用 `selected_rows` 作为行索引来选择 `selected_arr` 中的所有选定行，保持其原始的二维形状。

如果我们希望选择列而不是行，则可以使用 `axis=0` 参数来指定 `np.any` 或 `np.all` 函数在列上进行操作。例如，如果我们想选择所有至少包含一个选定元素的列，可以使用以下代码：

```
selected_cols = np.any(bool_arr, axis=0)
selected_arr = selected_arr[:, selected_cols]
```

这将选择 `selected_arr` 中所有选定列，并将结果存储在一个形状相同的数组 `selected_arr` 中。