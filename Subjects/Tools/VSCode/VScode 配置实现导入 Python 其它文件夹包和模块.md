---
title: VScode 配置实现导入 Python 其它文件夹包和模块
authors: Ethan Lin
year: 2024-12-19
tags:
  - 类型/笔记
  - 日期/2024-12-19
  - 类型/转载
  - 内容/编程语言/Python
  - 内容/vscode
aliases:
  - VScode 配置实现导入 Python 其它文件夹包和模块
---
# VScode 配置实现导入 Python 其它文件夹包和模块






> 原文链接：https://blog.csdn.net/qq_31654025/article/details/109474175



问题引入
  在使用vscode中，虽然vscode可以高度自定义，里面的一些插件确实也让自己难以自拔。但是，与Pycharm比较，还是有一些瑕疵的，比如说包以及模块的导入。在Pycharm中因为它是自己把工程目录给加入到sys.path中，所以在导包方面还是非常非常的nice de。

  如下面一个工程的结构，工程名是experiences，下面有parent包。parent包有parent_main.py以及child子包，child包里面有模块child_main.py。

```
experiences
--------parent
----------------parent_main.py
----------------child
------------------------child_main.py
parent_main.py
```


parent_main.py



```
def parent_main_func():
    print('parent_main_func')
if __name__ == '__main__':
    import sys
    for a_path in sys.path:
        print(a_path)
```



在Pycharm运行`parent_main.py`，可以得到结果：



```
D:\nwq\Documents\pythonProjects\experiences\parent
D:\nwq\Documents\pythonProjects\experiences
D:\miniconda3\python37.zip
D:\miniconda3\DLLs
D:\miniconda3\lib
D:\miniconda3
D:\miniconda3\lib\site-packages
D:\miniconda3\lib\site-packages\win32
D:\miniconda3\lib\site-packages\win32\lib
D:\miniconda3\lib\site-packages\Pythonwin

```

在vscode中运行`parent_main.py`，可以得到结果：

```
d:\nwq\Documents\pythonProjects\experiences\parent
D:\miniconda3\python37.zip
D:\miniconda3\DLLs
D:\miniconda3\lib
D:\miniconda3
D:\miniconda3\lib\site-packages
D:\miniconda3\lib\site-packages\win32    
D:\miniconda3\lib\site-packages\win32\lib
D:\miniconda3\lib\site-packages\Pythonwin

```



两者相比，很明显可以得到vscode中少了路径D:\nwq\Documents\pythonProjects\experiences，即工程路径。这使得在vscode导包时会不舒服。举个栗子，在child_main.py文件中在pycharm是可以导入parent模块的，但是在vscode中却会报：ModuleNotFoundError: No module named 'parent'错误。

child_main.py

```
if __name__ == "__main__":
    from parent.parent_main import parent_main_func
    parent_main_func()

```



解决问题之前的小小知识点
  vscode有两个重要的配置文件：launch.json和settings.json。launch.json是使用VS Code运行调试程序的启动设置，包括设置环境变量，使用哪个解释器，debug类型以及程序入口等等。settings.json是VS Code程序的设置选项，包括快捷键，插件设置等等。

  在Python中有一个环境变量PYTHONPATH，在得到模块搜索路径时，PYTHONPATH会和其他模块搜索路径连接在一起得到程序的模块/包搜索路径。所以下面的方法都是通过设置PYTHONPATH环境变量实现。

解决方案one，通过env设置PYTHONPATH环境变量
  因为launch.json可以设置环境变量，所以在launch.json可以添加PYTHONPATH环境变量，那么就可以实现把自己想要加入的包导入到路径中了。根据官网的介绍可以在launch.json中设置变量env来设置PYTHONPATH值，具体设置代码如下，重点是最后一行设置env项。

launch.json
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: 当前文件",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "env": {"PYTHONPATH":"${workspaceFolder};${env:PYTHONPATH}"}
        }
    ]
}

```



在.vscode文件夹下保存，按下F5键debug代码，那么就可以得到结果，而不是报找不到模块错误。：

parent_main_func
  但是当你点击右上角Run Python File in Terminal按钮时，还是会报错的哦。

解决方案two，通过envFile设置PYTHONPATH环境变量
  通过envFile设置PYTHONPATH环境变量既可以在launch.json也可以在settings.json中设置，下面是其设置的代码。envFile是一个存储变量的文件，后缀名为.env。${workspaceFolder}是工作空间文件夹路径名。

launch.json

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: 当前文件",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            // "env": {"PYTHONPATH":"${workspaceFolder};${env:PYTHONPATH}"}
            "envFile": "${workspaceFolder}/.env"
        }
    ]
}

```

settings.json

```
{
    "python.formatting.provider": "autopep8",
    "python.pythonPath": "D:\\miniconda3\\python.exe",
    "python.envFile": "${workspaceFolder}/.env",
}

```

.env

```
PYTHONPATH="${workspaceFolder};${env:PYTHONPATH}"

```

和设置env一样，可以debug运行，但是右上角Run Python File in Terminal按钮时，还是会报错的哦。

解决方案three，通过terminal.integrated.env.windows设置PYTHONPATH环境变量
terminal.integrated.env.windows设置的环境变量可以在VS Code运行的Windows终端下使用。是在settings.json文件中设置的。

settings.json

```
{
    "python.formatting.provider": "autopep8",
    "python.pythonPath": "D:\\miniconda3\\python.exe",
    // "python.envFile": "${workspaceFolder}/.env",
    "terminal.integrated.env.windows": {"PYTHONPATH":"${workspaceFolder};${env:PYTHONPATH}"}
}

```

无论你是按`F5`键`debug`还是点击右上角`Run Python File in Terminal`按钮，都可以很好地解决哦。

















































