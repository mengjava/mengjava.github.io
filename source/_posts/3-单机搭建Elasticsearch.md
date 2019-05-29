---
title: 3 单机搭建Elasticsearch
date: 2019-05-29 14:32:46
categories: 
- Elasticsearch专题
tags:
- es部署
---
####  1 安装JDK
自行百度

#### 2 安装Elasticsearch
##### 2.1 创建elasticsearch目录

```java
cd /usr/local/
mkdir tool
cd tool
mkdir elasticsearch
cd elasticsearch

```
<!--more-->

####  2.2 下载Elasticsearch
##### 2.2.1 在刚刚创建好的文件夹内下载Elasticsearch(以下简称es)
```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.4.2.tar.gz
```

##### 2.2.2 解压es包
```
tar -xvf elasticsearch-5.4.2.tar.gz
```
##### 2.2.3 进入es/bin包
```
cd elasticsearch-5.4.2/bin
```
##### 2.2.4 启动es
```
./elasticsearch
```

