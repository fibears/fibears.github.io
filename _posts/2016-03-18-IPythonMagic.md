---

layout: post
title: "IPython 魔法师"
subtitle: "\"Introduction!\""
date: 2016-03-18
author: "fibears"
header-img: "img/in-post/header/post-bg-text.png"
tags:
- Python
- Introduction

---



 [IPython](http://ipython.readthedocs.org/en/latest/) 即 `Interactive Python`，顾名思义，它设计的目的在交互式计算和软件开发这两个方面最大化地提高生产力。目前我的`Python`开发环境就是`Jupyter notebook`和`IPython+Sublime`的组合，先利用`Jupyter notebook`的可视化效果进行代码测试，然后再利用`IPython+Sublime`来完善代码的细节部分。
 
我想或许你早就听闻`IPython`的大名，毕竟著名的`Jupyter notebook`中也使用`IPyhton`来处理`Python`代码。那么问题来了，`IPython`到底有哪些强大之处呢？以下是几个我觉得比较实用的功能：

- `Tab`键自动补全功能
- `Introspection`(内省功能)
- `Traceback`(回溯功能) & 交互调试器
- `Magic Command`(魔术命令)
- 集成`matplotlib`(`pylab`模式)
- 集成`shell`命令
- 个性化配置
- 强大的`R`语言交互环境！

## Tab键自动补全功能

与标准的`Python shell`相比，`IPython`的主要改进功能之一就是 `Tab` 键自动补全命令功能。在`IPython shell`中只要按下`Tab`键，就可以获取当前空间中任何匹配的变量，此时结合[iTerm](http://www.iterm2.com)的命令选取功能简直太方便了！


## `Introspection`(内省功能)

类似于`R`语言中的`help`功能，我们只需在变量的前面或后面加上一个问号`?`即可获得该对象的相关信息。

## `Traceback`(回溯功能) & 交互调试器

如果使用魔法命令`%run`执行脚本时发生了异常，此时IPython会返回异常代码的位置并附上附近节点的代码。此时输入`%debug`命令会直接跳转到引发异常的代码位置。

## `Magic Command`(魔术命令)

`IPython`中有一些非常便利的命令，它们被称为魔术命令。这些命令的共同特点是以`%`为前缀，比如上文中提到的执行脚本命令`%run`。借助这些命令，可以大大地提高编程效率。以下是一些常用的魔术命令：

> %quickref： 显示IPython的快速参考
> 
> %magic： 显示所有魔术命令的详细文档
> 
> %debug： 交互调试器
> 
> %pdf：代码发生异常后自动进入调试器
> 
> %paste：执行剪贴板中的代码
> 
> %cpaste： 打开一个特殊提示符以便于手工粘贴待执行的代码
> 
> %xdel variable： 删除变量，并清除其在IPthon的对象上的一切引用
> 
> %run： 执行脚本文件

## 集成`matplotlib`(`pylab`模式)

`IPython`的另一个强大功能在于它和绘图库`matplotlib`无缝连接，我们可以直接在`IPython`中执行绘图命令。通常情况下，我们可以通过`ipython --pylab`来启动`IPython`的`pylab`模式。

## 集成`shell`命令

对于熟悉`shell`命令的同学来说，这个功能简直棒极了！`IPython`和操作系统`shell`结合地非常紧密，我们可以直接在`IPython`中执行一些`shell`常用命令，比如：

> %alias alias_name code: 定义别名
> 
> %bookmark: 目录书签系统，可以保存常用的目录名实现快速跳转
> 
> %cd: 切换工作路径
> 
> %pwd: 输出当前工作路径
> 
> %env: 以字典形式返回系统环境变量

## 个性化配置

当然也许你会觉得原始的配色不好看，行间距不合理 and so on...没事，`IPython`还提供了个性化配置的功能，我们可以通过修改配置文件(`ipython_config.py`)来实现。具体的个性化配置说明可以参阅[官方说明文档](http://ipython.readthedocs.org/en/latest/config/details.html)。


## `IPython`提供了强大的`R`语言交互环境

对于数据分析爱好者而言，`Python`和`R`是两个强大的工具，它们各有千秋。`Python`是一种通用的编程工具，而`R`具有更强的统计分析功能和可视化工具。那么问题来了，到底是学习R还是python，哪个工具更实用呢？这是一直被大家所争论的话题。

不要怕，现在我们可以利用`rpy2`和`rPython`这两个包将`Python`和`R`有机地结合起来。关于`rpy2`的详细功能，可以参考[官方说明文档](http://rpy2.readthedocs.org/en/version_2.7.x/)。虽然官方文档中的解释说明非常详细，但是我表示已经被各种引用符号搞混了。不过天无绝人之路，强大的`Jupyter notebook`还提供了给力的`rmagic`功能，我们可以利用`%R`(运行单行命令)或`%%R`(运行代码块)在`IPython`中运行原始的`R`脚本！

```python
# 首先需要安装rpy2
pip install rpy2

# 进入Jupyter notebook环境中并载入 rpy2.ipython
%load_ext rpy2.ipython

# 此时就可以利用rmagic命令运行R脚本
%%R
library(ggplot2)
qplot(mpg,disp, data= mtcars)

```

不过需要注意的是，`R`环境中的变量和`Python`环境中的变量是独立的，如果想要交互引用变量数据的话，需要利用命令`%%R -i data`(input data)和`%%R -o data`(output data)。


> **Reference**:
> [rpy2](http://rpy2.readthedocs.org/en/version_2.7.x/)
> [rmagic function](http://nbviewer.jupyter.org/github/ipython/ipython/blob/3607712653c66d63e0d7f13f073bde8c0f209ba8/docs/examples/notebooks/rmagic_extension.ipynb)
> [R&Python](http://nbviewer.jupyter.org/gist/xccds/d692e468e21aeca6748a)







