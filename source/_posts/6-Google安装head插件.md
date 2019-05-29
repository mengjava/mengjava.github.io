---
title: 6 Google安装head插件
date: 2019-05-29 15:01:27
categories: 
- Elasticsearch专题
tags:
- es-head插件
---

### 1 前言
通过前面的学习我们已经可以往elasticsearch中存数据了，我们知道elasticsearch天生就是为海量数据和大规模集群而存在的，所以如果我们想要管理这些数据和集群，那么肯定得借助其他的一些工具进行管理，不然大家可以想象一下，我想要知道我的索引有哪些，数据量有多大，还得发送一个一个REST去查。我想管理我elasticsearch集群中所有的主机，看看它们的运行状态，集群数量，还得一台一台机器远程连接进去。这个工作量我想任何一个运维人员都是受不了的，所有这个时候，如果有一个图形管理界面，可以让我们集中并很直观管理我们的数据和集群，那这个工具没有理由不使用把！而这个图形界面就是elasticsearch-head插件。
<!--more-->

###  2 elasticsearch-head插件介绍

elasticsearch-head是一个界面化的集群操作和管理工具，可以对集群进行傻瓜式操作。你可以通过插件把它集成到elasticsearch（5.0版本后不支持此方式）,也可以安装成一个独立webapp。elasticsearch-head插件是使用JavaScript开发的，依赖Node.js库，使用Grunt工具构建，所以等会我们要安装elasticsearch-head，还需要先安装Node.js和Grunt。

**elasticsearch-head主要的作用有以下这些方面：**

* 显示集群的拓扑,并且能够执行索引和节点级别操作
* 搜索接口能够查询集群中原始json或表格格式的检索数据能够快速访问并显示集群的状态有一个输入窗口,允许任意调用RESTful API。
* 这个接口包含几个选项,可以组合在一起以产生不同的结果;
* 请求方法(get、put、post、delete),查询json数据,节点和路径
* 支持JSON验证器支持重复请求计时器
* 支持使用javascript表达式变换结果


### 3 安装步骤


由于elasticsearch6.0以上版本安装head需要离线安装，而且集群安装比较麻烦使用google浏览器插件安装
[下载地址](https://github.com/TravisTX/elasticsearch-head-chrome)　　

![1.png](https://i.loli.net/2019/05/29/5cee26297b8fc98483.png)
**将解压的包，加载到google浏览器点击此处使用head插件**

![3.png](https://i.loli.net/2019/05/29/5cee2629f0b3046656.png)

![2.png](https://i.loli.net/2019/05/29/5cee2629ad78e13628.png)

![4.png](https://i.loli.net/2019/05/29/5cee2629c85bd91869.png)


