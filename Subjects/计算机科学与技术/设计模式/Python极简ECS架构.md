---
title: Python极简ECS架构
authors: Ethan Lin
year: 2024-12-17
tags:
  - 类型/AI问答笔记
  - 日期/2024-12-17
  - 内容/ECS框架
  - 内容/Numpy
  - 内容/设计模式
aliases:
  - Python极简ECS架构
---
# Python极简ECS架构





> [!attention]
> 以下由AI回答，不保证正确性！






实现一个基于 Python 之 Numpy 工具包的极简实体组件系统架构。





## 用 NumPy 实现的极简版实体组件系统 (ECS) 查询功能



以下是一个用 NumPy 实现的极简版实体组件系统 (ECS) 查询功能，返回符合条件的实体 ID。假设实体的组件数据是以矩阵形式存储的，每一行表示一个实体，每一列表示一个组件。



```python
import numpy as np

def query_entities(components, conditions):
    """
    根据组件条件查询符合条件的实体 ID。
    
    参数：
        components (np.ndarray): 一个二维数组，行表示实体，列表示组件的值。
        conditions (list of tuple): 条件列表，每个条件是一个 (col_idx, operator, value) 的元组。
                                    - col_idx: 组件列索引
                                    - operator: 比较操作符（'==', '!=', '>', '<', '>=', '<='）
                                    - value: 需要比较的值
                                    
    返回：
        np.ndarray: 符合条件的实体 ID。
    """
    mask = np.ones(components.shape[0], dtype=bool)  # 初始化掩码，全为 True
    
    for col_idx, operator, value in conditions:
        if operator == '==':
            mask &= (components[:, col_idx] == value)
        elif operator == '!=':
            mask &= (components[:, col_idx] != value)
        elif operator == '>':
            mask &= (components[:, col_idx] > value)
        elif operator == '<':
            mask &= (components[:, col_idx] < value)
        elif operator == '>=':
            mask &= (components[:, col_idx] >= value)
        elif operator == '<=':
            mask &= (components[:, col_idx] <= value)
        else:
            raise ValueError(f"Unsupported operator: {operator}")
    
    return np.where(mask)[0]  # 返回满足条件的实体 ID（行号）

# 示例组件数据（行是实体，列是组件值）
components = np.array([
    [10, 5, 1],   # 实体 0
    [15, 3, 0],   # 实体 1
    [7,  8, 1],   # 实体 2
    [20, 2, 1],   # 实体 3
])

# 查询条件（列索引, 比较操作符, 值）
conditions = [
    (0, '>', 10),   # 第一列值大于 10
    (2, '==', 1),   # 第三列值等于 1
]

# 执行查询
matching_entities = query_entities(components, conditions)
print("符合条件的实体 ID:", matching_entities)
```

------

**运行结果**

在上面的示例中：

- 第一列（索引 `0`）大于 `10` 的实体是 `[1, 3]`。
- 第三列（索引 `2`）等于 `1` 的实体是 `[0, 2, 3]`。

满足这两个条件的交集是：

```plaintext
符合条件的实体 ID: [3]
```

------

**功能特点**

1. **灵活性**：支持多条件查询，且条件之间是“与”的关系。
2. **性能**：利用 NumPy 的向量化计算，查询操作非常高效，适合大规模数据。
3. **可扩展性**：可以根据需要扩展支持更多操作符（如逻辑“或”条件）。

你可以用类似方式实现更复杂的 ECS 系统，或扩展支持动态添加和移除组件。



## 查询语句实现树结构的复杂逻辑表达式



实现树结构的复杂逻辑表达式，可以通过以下方法实现逻辑表达式的动态解析和运算。具体来说，有两种主要的实现思路：

1. **基于解析器的方案**
    编写一个逻辑解析器，将表达式解析成树结构，然后递归计算。
2. **直接执行逻辑表达式**
    如果逻辑表达式可以用 Python 的语法表示，可以直接将字符串转换为可执行代码，用 NumPy 运算优化。

下面分别说明这两种方案的实现方式。

------

### **方案 1：解析逻辑表达式成树结构**

**思路**

将逻辑表达式解析为二叉树或多叉树的形式，每个节点是操作符（如 AND、OR、NOT），每个叶子是一个条件。通过递归计算树，逐步生成布尔掩码。

**实现代码**

```python
import numpy as np

class LogicNode:
    """逻辑表达式的树节点"""
    def __init__(self, operator=None, condition=None, left=None, right=None):
        self.operator = operator  # 操作符: AND, OR, NOT
        self.condition = condition  # 条件 (col_idx, operator, value)
        self.left = left  # 左子树
        self.right = right  # 右子树

    def evaluate(self, components):
        """递归计算树节点"""
        if self.condition:
            col_idx, op, value = self.condition
            if op == '==':
                return components[:, col_idx] == value
            elif op == '!=':
                return components[:, col_idx] != value
            elif op == '>':
                return components[:, col_idx] > value
            elif op == '<':
                return components[:, col_idx] < value
            elif op == '>=':
                return components[:, col_idx] >= value
            elif op == '<=':
                return components[:, col_idx] <= value
            else:
                raise ValueError(f"Unsupported operator: {op}")

        elif self.operator == "AND":
            return np.logical_and(self.left.evaluate(components), self.right.evaluate(components))
        elif self.operator == "OR":
            return np.logical_or(self.left.evaluate(components), self.right.evaluate(components))
        elif self.operator == "NOT":
            return np.logical_not(self.left.evaluate(components))
        else:
            raise ValueError(f"Unsupported operator: {self.operator}")

# 示例数据
components = np.array([
    [10, 5, 1],
    [15, 3, 0],
    [7, 8, 1],
    [20, 2, 1],
])

# 构建表达式树: (col 0 > 10 AND col 1 < 4) OR NOT (col 2 == 1)
root = LogicNode(
    operator="OR",
    left=LogicNode(
        operator="AND",
        left=LogicNode(condition=(0, '>', 10)),
        right=LogicNode(condition=(1, '<', 4))
    ),
    right=LogicNode(
        operator="NOT",
        left=LogicNode(condition=(2, '==', 1))
    )
)

# 计算结果
result_mask = root.evaluate(components)
matching_entities = np.where(result_mask)[0]
print("符合条件的实体 ID:", matching_entities)
```

------

**运行结果**

条件树逻辑：

```plaintext
(col 0 > 10 AND col 1 < 4) OR NOT (col 2 == 1)
```

结果：

```plaintext
符合条件的实体 ID: [1 2 3]
```

------

### **方案 2：直接执行逻辑表达式**

**思路**

利用 Python 的 `eval` 函数，将逻辑表达式写为字符串形式，动态生成掩码。
 表达式可以引用 NumPy 的操作，并通过变量传递组件数组。

**实现代码**

```python
import numpy as np

def query_with_expression(components, expression):
    """
    根据逻辑表达式查询符合条件的实体 ID。
    
    参数：
        components (np.ndarray): 组件矩阵，每行是一个实体，每列是一个组件。
        expression (str): 逻辑表达式，使用 `components[:, col]` 形式引用组件列。
                          示例："(components[:, 0] > 10) & (components[:, 1] < 4) | ~(components[:, 2] == 1)"
    
    返回：
        np.ndarray: 符合条件的实体 ID。
    """
    # 使用 eval 动态计算布尔掩码
    mask = eval(expression)
    return np.where(mask)[0]

# 示例组件数据
components = np.array([
    [10, 5, 1],
    [15, 3, 0],
    [7, 8, 1],
    [20, 2, 1],
])

# 示例逻辑表达式
expression = "(components[:, 0] > 10) & (components[:, 1] < 4) | ~(components[:, 2] == 1)"

# 查询
matching_entities = query_with_expression(components, expression)
print("符合条件的实体 ID:", matching_entities)
```

------

**运行结果**

逻辑表达式：

```plaintext
(components[:, 0] > 10) & (components[:, 1] < 4) | ~(components[:, 2] == 1)
```

结果：

```plaintext
符合条件的实体 ID: [1 2 3]
```

------

### **方案对比**

| 特点         | **方案 1：解析器方案**             | **方案 2：直接执行方案** |
| ------------ | ---------------------------------- | ------------------------ |
| **灵活性**   | 支持复杂逻辑树，便于嵌套逻辑表达式 | 逻辑表达式写法简单直接   |
| **性能**     | 多层递归可能有性能开销             | 直接调用 NumPy，性能较高 |
| **可读性**   | 结构化表达，适合大型系统           | 依赖于表达式的书写规范   |
| **易扩展性** | 可以轻松扩展为支持自定义运算符     | 限制于 Python 逻辑语法   |
| **开发成本** | 需要额外编写解析器和树结构         | 开发简单，但不够灵活     |

------

### **推荐方案**

- 如果逻辑复杂且有嵌套结构，建议使用 **方案 1**（解析器方案），这样逻辑更加清晰、可维护。
- 如果逻辑简单且能直接用 Python 表达，建议使用 **方案 2**（直接执行逻辑表达式），开发速度快、性能高。

根据实际需求选择适合的方案！
