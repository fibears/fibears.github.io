---

layout: post
title: "Spark安装简介"
subtitle: "\"Introduction!\""
date: 2017-11-14
author: "fibears"
header-img: "img/in-post/header/post-bg-spark.jpg"
tags:
- Machine Learning
- Tools
- Spark

---


由于近期工作上需要用Spark处理一些建模任务，所以打算完整地学习下Spark的原理和使用方法。本系列文章主要记录学习Spark过程中的一些总结和心得体会，希望通过这些文章能够使得自己对该工具有深入的理解。

Spark是基于内存计算的大数据并行计算框架，在大数据环境下Spark不仅能实时处理数据，而且还保证了高容错性和高可伸缩性，用户可以将Spark部署在大量的廉价硬件之上，形成大规模的计算集群。

工欲善其事，必先利其器。本篇文章作为Spark学习笔记的第一篇，主要介绍如何在Mac安装Spark环境。

## Homebrew介绍

linux系统有个让人蛋疼的通病——软件包依赖，幸好当前主流的两大发行版本都自带了解决方案，Red hat有yum，Ubuntu有apt-get。[Homebrew](https://brew.sh)简称brew，它是Mac OS上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件。这么好用的工具安装起来也特别简单，仅仅只需要在终端中运行一行代码即可：

```
# install
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# example
brew install wget
```

## Spark安装过程

Spark的安装分为几种模式，其中一种是本地运行模式，只需要在单节点上解压即可运行，这种模式不需要依赖Hadoop 环境。本文主要介绍如何安装并使用单机版的Spark工具，我们可以直接到[Spark官网](http://spark.apache.org/)去下载安装包，也可以利用homebrew一键安装Spark。

### xcode安装

为了利用命令行一键安装软件，我们需要安装xcode和命令行开发工具：

```
# install xcode--open terminal and run next line code
xcode-select --install

# click install button and continue...
```

### 安装java环境和scala

由于Spark的开发语言scala依赖于java环境，因此我们需要预先安装好相应的java环境

```
# 更新brew版本
brew upgrade && brew update

# install java
brew install java

# install scala
brew install scala
```

### 安装Spark并配置路径

安装完java和scala后，我们就可以直接安装Apache-Spark了：

```
# install spark
brew install apache-spark

# update zsh or bash conf
# open .zshrc or .bash_profile file and add two conf information
export SPARK_HOME=/usr/local/Cellar/apache-spark/<your_spark_version>/libexec
export PYTHONPATH=/usr/local/Cellar/apache-spark/<your_spark_version>/libexec/python/:$PYTHONP$

```


### 环境测试

安装完Spark后，我们用一个简单的案例来测试是否可用：

```
# open spark shell environment
spark-shell

Welcome to

      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 2.2.0
      /_/
         
         
Using Scala version 2.11.8 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_101)

Type in expressions to have them evaluated.

Type :help for more information.

scala> val s = "hello world"

s: String = hello world

```














