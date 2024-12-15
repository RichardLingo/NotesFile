---
title: MarkItDown 转换各种格式到 markdown
authors: Ethan Lin
year: 2024-12-15
tags:
  - 类型/笔记
  - 日期/2024-12-15
  - 内容/Markdown
  - 内容/自动办公
  - 来源/转载
aliases:
---

> 来源：[MarkItDown: Python一站式文档转Markdown神器](https://blog.axiaoxin.com/post/python-markitdown/)

# MarkItDown 转换各种格式到 markdown


在日常开发或数据分析工作中，我们经常需要处理各种格式的文档，如 PDF、PowerPoint、Word 等。本文要介绍的这个由微软开源的 Python 工具库 [MarkItDown](https://github.com/microsoft/markitdown)，就是一个能够将各种格式文件转换为 Markdown 的强大工具，特别适合用于文本分析、内容索引和文档转换等场景。

## MarkItDown 的功能特点

MarkItDown 支持多种文件格式的转换：

- PDF 文件（.pdf）
- PowerPoint 演示文稿（.pptx）
- Word 文档（.docx）
- Excel 表格（.xlsx）
- 图片（支持提取 EXIF 元数据和 OCR 文字识别）
- 音频文件（支持提取元数据和语音转文字）
- HTML 网页（对 Wikipedia 等网站有特殊优化）
- 其他文本格式（csv、json、xml 等）

GitHub 仓库地址：[https://github.com/microsoft/markitdown](https://github.com/microsoft/markitdown)

## 环境准备

MarkItDown 要求 Python 3.10 或更高版本。这里提供几种环境配置方案：

### 使用 virtualenv

```bash
# 创建虚拟环境
virtualenv -p python3.10 env

# 激活虚拟环境
# Linux/macOS:
source venv/bin/activate
# Windows:
.\venv\Scripts\activate

# 安装MarkItDown
pip install markitdown
```

相关阅读：[Python 虚拟环境工具 virtualenv 详解与使用教程](https://blog.axiaoxin.com/post/python-virtualenv/)

### 使用 pipenv

```bash
# 创建并激活环境
pipenv --python 3.10
pipenv shell
pipenv install markitdown
```

相关阅读：[pipenv 用法详解：如何使用 pipenv 管理现代 Python 项目的虚拟环境和 requirements.txt 文件](https://blog.axiaoxin.com/post/how-to-use-pipenv/)

## MarkItDown 的使用方法

### 1. 基础文件转换

```python
from markitdown import MarkItDown

# 初始化转换器
markitdown = MarkItDown()

# 转换不同类型的文件
# PDF文档
pdf_result = markitdown.convert("annual_report.pdf")
print(pdf_result.text_content)

# Word文档
docx_result = markitdown.convert("meeting_notes.docx")
print(docx_result.text_content)

# Excel表格
xlsx_result = markitdown.convert("sales_data.xlsx")
print(xlsx_result.text_content)
```

### 2. 处理网络资源

```python
# 直接从URL转换
url_result = markitdown.convert("https://example.com/document.pdf")
print(url_result.text_content)

# 处理HTTP响应
import requests
response = requests.get("https://api.example.com/document")
response_result = markitdown.convert(response)
print(response_result.text_content)
```

### 3. 处理流式数据

```python
with open("example.pdf", "rb") as f:
    result = markitdown.convert_stream(f)
    print(result.text_content)
```

### 4. 命令行使用

MarkItDown 提供了便捷的命令行工具，支持多种输入方式：

```bash
# 直接转换文件
markitdown example.pdf > output.md

# 通过管道输入
cat example.pdf | markitdown > output.md

# 通过重定向输入
markitdown < example.pdf > output.md
```

## MarkItDown 高级特性

### 1. 自定义会话和模型

```python
import requests
from markitdown import MarkItDown

# 自定义requests会话
session = requests.Session()
session.headers.update({'User-Agent': 'Custom User Agent'})

markitdown = MarkItDown(
    requests_session=session,
    mlm_client=your_custom_client,  # 用于OCR或语音识别
    mlm_model=your_custom_model
)
```

### 2. 自定义转换器

```python
from markitdown import MarkItDown, CustomConverter

markitdown = MarkItDown()
# 注册自定义转换器处理特定文件类型
markitdown.register_page_converter(CustomConverter())
```

## 异常处理最佳实践

```python
try:
    result = markitdown.convert("document.pdf")
    print(result.text_content)
except FileNotFoundError:
    print("文件不存在，请检查路径")
except Exception as e:
    print(f"转换失败: {str(e)}")
```

## 使用建议

1. **文件类型识别**：如遇到文件类型识别问题，可以明确指定扩展名：
    
    ```python
    result = markitdown.convert("document", file_extension=".pdf")
    ```
    
2. **内存管理**：MarkItDown 会自动管理临时文件，处理大文件时无需特别处理。
3. **性能优化**：处理批量文件时，建议复用同一个 MarkItDown 实例：
    
    ```python
    markitdown = MarkItDown()
    results = [markitdown.convert(file) for file in files]
    ```
    

## 总结

MarkItDown 作为一个强大的文档转换工具，无论是在自动化文档处理、内容分析还是数据提取场景中，MarkItDown 都是一个值得收藏的工具。它不仅能满足基础的文档转换需求，还能通过其强大的扩展性满足各种特殊需求。
