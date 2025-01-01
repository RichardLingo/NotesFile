---
title: VSCode Python 配置
authors: Ethan Lin
year: 2024-12-30
tags:
  - 类型/笔记
  - 日期/2024-12-30
  - 类型/举例
  - 类型/查阅
  - 类型/指南
  - 内容/vscode
  - 内容/配置
  - 内容/VSCode配置
aliases:
  - VSCode Python 配置
---
# VSCode 配置实例



## VSCode Python 配置实例




`.env` 文件：

```
PYTHONPATH=../SRS:./my_lab_code:${PYTHONPATH}
```


`.vscode/launch.json` 文件：

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Current File",
            "type": "debugpy",
            "request": "launch",
            "program": "${file}",
            "stopOnEntry": false,
            "cwd": "${fileDirname}",
            "console": "integratedTerminal",
            "justMyCode": true,
            "env": {
                "PYTHONPATH": "${workspaceFolder};${env:PYTHONPATH}",
            },
            "envFile": "${workspaceFolder}/.env"
        },
        {
            "name": "Python: Project Main File",
            "type": "debugpy",
            "request": "launch",
            "program": "${workspaceFolder}/PyScripts/exp_raw/main.py",
            "stopOnEntry": false,
            "cwd": "${workspaceFolder}",
            "console": "integratedTerminal",
            "justMyCode": true,
            "env": {
                "PYTHONPATH": "${workspaceFolder}"
            },
            "envFile": "${workspaceFolder}/.env"
        },
        {
            "name": "Python: Project Visualize Data File",
            "type": "debugpy",
            "request": "launch",
            "program": "${workspaceFolder}/PyScripts/visualization/visualize_data.py",
            "stopOnEntry": false,
            "cwd": "${workspaceFolder}",
            "console": "integratedTerminal",
            "justMyCode": true,
            "env": {
                "PYTHONPATH": "${workspaceFolder}"
            },
            "envFile": "${workspaceFolder}/.env"
        },
        {
            "name": "Python: File",
            "type": "debugpy",
            "request": "launch",
            "program": "${file}",
            "stopOnEntry": true,
            "cwd": "${workspaceFolder}",
            "console": "integratedTerminal",
            "justMyCode": true
        },
        // {
        //     "type": "julia",
        //     "request": "launch",
        //     "name": "Launch Program",
        //     "program": "${workspaceFolder}/Program",
        //     "stopOnEntry": false
        // },
        {
            "type": "julia",
            "request": "launch",
            "name": "Run active Julia file",
            "program": "${file}",
            "stopOnEntry": false,
            "cwd": "${workspaceFolder}",
            "juliaEnv": "${command:activeJuliaEnvironment}"
        },
        {
            "type": "julia",
            "request": "launch",
            "name": "Launch main.jl file",
            "program": "${workspaceFolder}/JuliaScripts/exp_raw/main.jl",
            "stopOnEntry": false,
            "cwd": "${workspaceFolder}",
            "juliaEnv": "${command:activeJuliaEnvironment}"
        }
    ]
}
```



`...code-workspace` 文件：



```
{
	"folders": [
		{
			"name": "SRL",
			"path": "."
		},
		{
			"name": "SRS",
			"path": "../SRS"
		},
		{
			"name": "ray_results",
			"path": "../../../ray_results"
		},
		{
			"path": "../../CodesFile/ecm/RSRwithDDPG"
		}
	],
	"settings": {
		"git.ignoreLimitWarning": true,
		"python.autoComplete.extraPaths": [
			"../SRS/SRS",
			"../../CodesFile/ecm/RSRwithDDPG",
			"./my_lab_code",
		],
		"terminal.integrated.env.osx": {
			"PYTHONPATH": "${workspaceFolder};${env:PYTHONPATH}"
		},
		"files.autoSaveWhenNoErrors": true,
		"files.autoSaveWorkspaceFilesOnly": true,
		"files.autoSave": "onWindowChange"
	},
	"launch": {
		"version": "0.2.0",
		"configurations": []
	}
}
```







# 其他资源

- [[VScode 配置实现导入 Python 其它文件夹包和模块]]





