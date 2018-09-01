---
title: "我的VSCode个性化设置"
date: 2018-06-05 20:12:00
layout: post
tags: 
  - python
  - vscode
---

第一次使用VScode写python代码，比我想象中要好得多，很方便。不过在使用之前，还是看了很多YouTube视频，看看其他人都是怎么配置vscode的。好在用vscode尝试学习了flask框架，感觉挺好，自动补全，错误提示，格式化代码等，很方便。

![](https://krazel-1256848099.cos.ap-chengdu.myqcloud.com/img/TIM-20180605201127.png)


<!--more-->


特地将我的个性化设置保存下来，以免下次使用的时候无法使用。对于项目本身，可以使用工作区设置，只要设置好virtualenv的python路径就好，然后就会自动下载全局个性化设置中配置的项目，比如flake8，pylint，autopep8等python包。


```json
{
    "files.insertFinalNewline": true,
    "editor.lineHeight": 20,
    "editor.fontSize": 16,
    "workbench.colorTheme": "One Monokai",
    "team.showWelcomeMessage": false,
    "markdownlint.config": {
        "MD002": false
    },
    "python.pythonPath": "C:\\Python36\\python.exe",
    "python.linting.flake8Enabled": true,
    "python.formatting.provider": "autopep8",
    "editor.formatOnSave": true,
    "editor.formatOnType": true,
    "python.linting.pylintArgs": [
        "--disable=C0111,C0301,C0103"
    ],
    "workbench.iconTheme": "material-icon-theme",
    "explorer.confirmDelete": false
}
```
