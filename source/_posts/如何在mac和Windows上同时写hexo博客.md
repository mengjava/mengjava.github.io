---
title: work for mac
date: 2018-12-12 21:32:24
categories: 
- 博客搭建
tags:
- 博客
- hexo
---
首先，我是在公司上的windows机器上部署了hexo，但是回家后发现很不方便，家里的电脑是mac机。所以根据网上的经验我总结了一下如何在mac和Windows上同时写hexo博客。

# Windows机器

### 在github上新建远程仓库

将原来的page项目删除，新建一个和原来名字一样的空项目。<font color=red size=3>不用初始化README.md</font>
 此时只有一个空的master分支。



### 本地初始化一个Hexo项目
<font color=red size=3>注意：本地的目录不要动</font>

重新新建一个空目录，作为你的博客目录。进入该目录，右击Git bash here，初始化一个Hexo项目：
```
hexo init
npm install
npm install hexo-deployer-git --save
```
然后用自己原来博客里的文件替换掉这里的**source\, scaffolds\, themes\,_config.yml**替换成自己原来博客里的。
<font color=red size=3>注意，这里把**themes/next**中的**.git/**目录删除</font>

### 将整个目录推送到master

要推送到master分支，首先要将该目录初始化为本地Git仓库：



```
git init
//把博客目录下所有文件推送到master分支
git remote add origin xx
git add .
git commit -m "first add hexo source code"
git push origin master

```


注意：**如果不小心初始化了README.md 在执行 git push origin master 会失败.。此时先执行以下命令进行代码合并**

```
git push origin master
```

 

### 在github上新建一个分支

    1.创建本地分支
     
    git branch 分支名，例如：git branch hexo
     
    2.切换本地分支
     
    git checkout 分支名，例如从master切换到分支：git checkout hexo
     
    3.远程分支就是本地分支push到服务器上。比如master就是一个最典型的远程分支（默认）。
     
    git push origin hexo
     
    4.设置默认分支 
    git branch --set-upstream-to=origin/hexo hexo
    
新建一个分支hexo(名字可以自定义)，这时候hexo分支和master分支的内容一样，都是hexo的源文件。
并把hexo设为默认分支，这样的话在另外一台机器上克隆下来就直接进入hexo分支，并且以后所有操作都是在hexo分支下完成。

**为什么需要这个额外的分支呢？**

因为hexo d只把静态网页文件部署到master分支上，所以你换了另外一台电脑，就无法pull下来继续写博客了。有了hexo分支的话，就可以把hexo分支中的源文件(配置文件、主题样式等)pull下来，再hexo g的话就可以生成一模一样的静态文件了



# 部署博客

还是和以前一样：

    1. hexo g -d

    如果提示 RROR Deployer not found: git 说明前面 npm install --save hexo-deployer-git 没有执行成功 再执行一次
     
    2. npm install --save hexo-deployer-git
    博客已经成功部署到master分支，这时候到github查看两个分支的内容，hexo分支里是源文件，master里是静态文件
    
    注意：根目录下的_config.yml配置文件中branch一定要填master，否则hexo d就会部署到hexo分支下。

 

### 关联到远程hexo分支

如果有修改的话 ,在本地新建一个hexo分支别忘了，要推送到hexo分支上去：

    git add .
    git commit -m  ""
    git push origin hexo

这样才能在另外的机器上pull下来，保持同步

 

# MAC机器

个人PC上的工作已经完成了，下面讲一下如果你换到了另外一台电脑上，应该如何操作。

将博客项目克隆下来: git clone xxx

克隆下来的仓库可能是master分支（**我已经设置hexo为默认分支了 但是clone下来还是master分支**）这时候需要切换一下分支，所以可以在这基础上继续写博客了。

    #查看所有分支
    git branch -a
    #切换分支
    git checkout hexo


但是由于.gitignore文件中过滤了node_modules\，所以克隆下来的目录里没有node_modules\，这是hexo所需要的组件，所以要在该目录中重新安装hexo，但不需要**hexo init**。

    npm install hexo
    npm install
    npm install hexo-deployer-git --save

**新建一篇文章测试**

    hexo g -d

**推送到hexo分支**

    git add .
    git commit -m "add work PC test"
    git push origin hexo

**部署到master分支**

    hexo g -d

**日常操作**
如果上面的过程都操作无误的话，你就可以在任何能联网的电脑上写博客啦。一般写博客的流程是下面这样。

**写博客前**
不管你本地的仓库是否是最新的，都先pull一下，以防万一：

    git pull origin hexo

 

把最新的pull下来，再开始撰写新的博客。

**写博客**

hexo new "title"
然后打开source/_posts/title.md，撰写博文。

**写完博客**

先推送到hexo分支上：

    git add .
    git commit -m "add article xxx"
    git push origin hexo

**最后部署到master分支上**

    hexo g -d

整个流程大概就是这样。


--------------------- 




