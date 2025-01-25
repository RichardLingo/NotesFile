---
title: Pandas 关于视图和副本
authors: Ethan Lin
year: 2025-01-17
tags:
  - 类型/AI问答笔记
  - 日期/2025-01-17
  - 类型/辨析
  - 内容/pandas
  - 内容/处理数据
aliases:
  - Pandas 关于视图和副本
---
# Pandas 关于视图和副本





> [!attention]
> 以下由AI回答，不保证正确性！






遍历 `pandas` 数据框的方式及其返回的内容：

1. **`iterrows()`**:
   - 返回：行索引和行数据的副本（`Series`）。
   - 修改行数据不会影响原始数据框。

2. **`itertuples()`**:
   - 返回：行数据的命名元组（`namedtuple`），是数据的副本。
   - 修改命名元组不会影响原始数据框。

3. **`apply()`**:
   - 返回：函数应用后的结果。
   - 可以选择按行或按列应用函数，修改后的结果可以影响原始数据框（如果赋值回去）。

4. **`iteritems()`**:
   - 返回：列名和列数据的副本（`Series`）。
   - 修改列数据不会影响原始数据框。

5. **`items()`**:
   - 返回：列名和列数据的视图（`Series`）。
   - 修改列数据会影响原始数据框。

示例代码：

```python
import pandas as pd

# 创建示例数据框
df = pd.DataFrame({
    'A': [1, 2, 3],
    'B': [4, 5, 6]
})

# 使用 iterrows()
for index, row in df.iterrows():
    print(row['A'], row['B'])

# 使用 itertuples()
for row in df.itertuples():
    print(row.A, row.B)

# 使用 apply()
df['C'] = df.apply(lambda row: row['A'] + row['B'], axis=1)

# 使用 iteritems()
for col_name, col_data in df.iteritems():
    print(col_name, col_data)

# 使用 items()
for col_name, col_data in df.items():
    print(col_name, col_data)
```

总结：
- `iterrows()` 和 `itertuples()` 返回的是副本，修改它们不会影响原始数据框。
- `apply()` 可以影响原始数据框（如果赋值回去）。
- `iteritems()` 返回的是副本，`items()` 返回的是视图，修改 `items()` 返回的列数据会影响原始数据框。
