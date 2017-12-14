---
title: mongoDB安装
date: 2017-12-5 14:13:23
tags: 工具
category: "工具"  # 分类为 工具
---

### 安装
[MongoDB官网](https://www.mongodb.com/)下选择 try it free进行下载。双击安装

### 启动
#### 1) 前提准备
1. 将 `mongo.exe` 所在目录 加入到系统环境变量当中
2. 数据存放：在`MongoDB`安装目录下面新建文件夹 `mkdir data\db` 
3. 日志存放：在`MongoDB`安装目录下面新建文件夹 `mkdir log`，并新建文件 `mongod.log` 

#### 2) 双击 安装目录下的 `mongod.exe` 直接启动服务
#### 3) 命令行启动服务

##### 1. 命令行直接运行   
`mongod.exe --dbpath="C:\Program Files\MongoDB\data\db"`   
后面的文件路径既是 之前创建好的 `data\db`

##### 2. 通过配置文件启动服务
* c盘根目录下创建文件 `mongod.cfg`。在其中配置好要输出的 data 与 log
    ```
    systemLog:
      destination: file
      path: C:\Program Files\MongoDB\log\mongod.log
    storage:
      dbPath: C:\Program Files\MongoDB\data\db
    ```
* 命令行通过配置文件启动服务 `mongod --config c:\mongod.cfg`

##### 3. 通过安装服务来启动
* 命令行直接输入命令来安装  
`mongod.exe --dbpath="C:\Program Files\MongoDB\data\db" --logpath="C:\Program Files\MongoDB\log\mongod.log" --install`
* 通过上面创建好的配置文件来安装  
`mongod --config c:\mongod.cfg --install` 
* 启动MongoDB服务  
`net start MongoDB`
* 关闭MongoDB服务  
`net stop MongoDB`

### 进入mongodb
命令行直接敲入 `mongo` 即可

### 注意事项
1. Windows 10 的 C盘下面有些权限的问题。可将MongoDB文件夹的各项权限提高。
2. 安装服务不能成功的，以管理员身份运行安装下即可

### 参考资料
1. [JasonCeng_的博客](http://blog.csdn.net/Jason_first/article/details/75439043)
2. [Windows 平台安装MongoDB](http://www.runoob.com/mongodb/mongodb-window-install.html)
3. [lsc183](http://www.cnblogs.com/lsc183/archive/2012/08/16/mongodb.html)
