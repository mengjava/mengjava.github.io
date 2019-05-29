---
title: 4 Linux安装Kibana
date: 2019-05-29 15:01:00
categories: 
- Elasticsearch专题
tags:
- Kibana
---


#### 1 安装JDK
自行百度

#### 2 安装Kibana

#####  2.1 下载Kibana
这一步需要下载与Elasticsearch版本相同的kibana，本人是从本地上传到服务器上的，使用的是5.4.2。
<!--more-->
##### 2.2 解压Kibana

在服务器上传目录解压kibana：

```js
tar -zxvf kibana-5.4.2-linux-x86_64.tar.gz
```
##### 2.3 修改配置

修改config/kibana.yml文件配置：

```
vim kibana.yml
```

kibana.yml常见配置项

```js
# pingElasticsearch超时时间
elasticsearch.pingTimeout
# 读取Elasticsearch数据超时时间
elasticsearch.requestTimeout  
#Elasticsearch主机地址
elasticsearch.url: "http://ip:9200" 
# 允许远程访问
server.host: "0.0.0.0" 
# Elasticsearch用户名 这里其实就是我在服务器启动Elasticsearch的用户名
elasticsearch.username: "es"  
# Elasticsearch鉴权密码 这里其实就是我在服务器启动Elasticsearch的密码
elasticsearch.password: "es"  
```

根据情况增加配置即可，本人测试使用的是如下：
```js
#Elasticsearch主机地址
elasticsearch.url: "http://ip:9200" 
# 允许远程访问
server.host: "0.0.0.0" 
# Elasticsearch用户名 这里其实就是我在服务器启动Elasticsearch的用户名
elasticsearch.username: "es"  
# Elasticsearch鉴权密码 这里其实就是我在服务器启动Elasticsearch的密码
elasticsearch.password: "es"  
```

##### 2.4 启动Kibana

直接启动，进入bin目录后

```
./kibana
```

后台启动，进入bin目录后

```
nohup ./kibana &
```
##### 2.5 访问管理页面

访问http://ip:5601，可以看到如下页面：