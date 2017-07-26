---
title: HEXO+Github,搭建属于自己的博客
date: 2017-07-15 14:13:23
tags: hexo
category: "hexo"  # 分类为 hexo
---




### **配置环境**

[安装Node](https://nodejs.org/)（必须）
作用：用来生成静态页面的
到Node.js官网下载相应平台的最新版本，一路安装即可。

[安装Git](https://git-scm.com/)（必须）
作用：把本地的hexo内容提交到github上去.
安装Xcode就自带有Git，我就不多说了,windows就去官网下载git。

[申请GitHub](https://github.com/)（必须）
作用：是用来做博客的远程创库、域名、服务器之类的，怎么与本地hexo建立连接等下讲。
github账号我也不再啰嗦了,没有的话直接申请就行了，跟一般的注册账号差不多，SSH Keys，看你自己了，可以不配制，不配置的话以后每次对自己的博客有改动提交的时候就要手动输入账号密码，配置了就不需要了，怎么配置我就不多说了，网上有很多[教程](https://jingyan.baidu.com/article/a65957f4e91ccf24e77f9b11.html)。

<!-- more -->

### **正式安装Hexo**

Node和Git都安装好后,首先创建一个文件夹,如blog,用户存放hexo的配置文件,然后进入blog里安装Hexo。

执行如下命令安装Hexo：
``` bash
 $ npm install -g hexo
```

初始化然后，执行init命令初始化hexo,命令：
``` bash
$ hexo init
```

好啦，至此，全部安装工作已经完成！blog就是你的博客根目录，所有的操作都在里面进行。

生成静态页面
``` bash
$ hexo generate
```
（hexo g也可以）

本地启动

启动本地服务，进行文章预览调试，命令：
``` bash
$ hexo server
```
（hexo s也可以）

浏览器输入http://localhost:4000

我不知道你们能不能，反正我不能，因为我还有环境没配置好

### 配置Github

建立Repository

建立与你用户名对应的仓库，仓库名必须为【your_user_name.github.io】，固定写法

然后建立关联，我的blog在本地/Users/leopard/blog，blog是我之前建的东西也全在这里面，有：
``` bash
     _config.yml    node_modules    public      source

    db.json        package.json    scaffolds  themes
```

现在我们需要_config.yml文件，来建立关联，命令：
``` bash
vim _config.yml
```

翻到最下面，改成我这样子的
``` bash
    deploy:

     type: git

     repo: https://github.com/zhu18/zhu18.github.io.git

     branch: master
```

然后执行命令：

``` bash
npm install hexo-deployer-git --save
```

网上会有很多说法，有的type是github, 还有repository最后面的后缀也不一样，是github.com.git，我也踩了很多坑，我现在的版本是hexo: 3.1.1，执行命令hexo -vsersion就出来了,貌似3.0后全部改成我上面这种格式了。

忘了说了，我没用SSH Keys如果你用了SSH Keys的话直接在github里复制SSH的就行了，总共就两种协议，相信你懂的。

然后，执行配置命令：

``` bash
hexo deploy
```

然后再浏览器中输入[http://zhu18.github.io/](http://zhu18.github.io/)就行了，我的github的账户叫zhu18,把这个改成你github的账户名就行了

部署步骤

每次部署的步骤，可按以下三步来进行。
``` bash
    hexo clean

    hexo generate

    hexo deploy

    或者 hexo g -d
```

一些常用命令：

``` bash
    hexo new "postName" #新建文章

    hexo new page"pageName" #新建页面

    hexo generate #生成静态页面至public目录

    hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）

    hexo deploy #将.deploy目录部署到GitHub

    hexo help # 查看帮助

    hexo version #查看Hexo的版本
```

### **报错总结**


**ERROR Deployer not found: git** 或者 **ERROR Deployer not found: github**


**解决方法**： **npm install hexo-deployer-git --save**

如发生报错： **ERROR Process failed: layout/.DS_Store **, 那么进入主题里面layout和_partial目录下，使用删除命令：

**rm-rf.DS_Store**

ERROR Plugin load failed: hexo-server

原因：

Besides,utilities are separated into a standalone module.hexo.util is not reachable anymore.

解决方法，执行命令：

sudo npm install hexo-server

执行命令hexo server，提示：Usage: hexo ....

原因：

我认为是没有生成本地服务

解决方法，执行命令：

npm install hexo-server --save

提示：hexo-server@0.1.2 node_modules/hexo-server

....

表示成功了[参考](https://hexo.io/zh-cn/docs/server.html)

这个时候再执行：

hexo-server

得到:

INFOHexois running at http://0.0.0.0:4000/.PressCtrl+C to stop.

这个时候再点击[http://0.0.0.0:4000](http://0.0.0.0:4000/)，正常情况下应该是最原始的画面，但是我看到的是：

白板和Cannot GET / 几个字

原因：

由于2.6以后就更新了，我们需要手动配置些东西，我们需要输入下面三行命令：

npm install hexo-renderer-ejs --save

npm install hexo-renderer-stylus --save

npm install hexo-renderer-marked --save

这个时候再重新生成静态文件，命令：

hexo generate（或hexo g）

启动本地服务器：

hexo server（或hexo s）

再，点击网址[http://0.0.0.0:4000](http://0.0.0.0:4000/)OK终于可以看到属于你自己的blog啦，😄，虽然很简陋，但好歹有了一个属于自己的小窝了。

### 主题推荐

这里有大量的[主题列表](https://github.com/hexojs/hexo/wiki/Themes)使用方法里面

都有详细的介绍，我就不多说了。

我这里有几款个人认为不错的主题，免去你们，一个一个的选了，欢迎吐槽我的审美，😄

[Cover](https://github.com/daisygao/hexo-themes-cover)- A chic theme with facebook-like cover photo

[Oishi](https://github.com/henryhuang/oishi)- A white theme based on Landscape plus and Writing.

[Sidebar](https://github.com/hardywu/hexo-theme-sidebar)- Another theme based on Light with a simple sidebar

[TKL](https://github.com/SuperKieran/TKL)- A responsive design theme for Hexo. 一个设计优雅的响应式主题

[Tinnypp](https://github.com/levonlin/Tinnypp)- A clean, simple theme based on Tinny

[Writing](https://github.com/yunlzheng/hexo-themes-writing)- A small and simple hexo theme based on Light

[Yilia](https://github.com/litten/hexo-theme-yilia)- Responsive and simple style 优雅简洁响应式主题，我用得就是这个。

[Pacman voidy](https://github.com/Voidly/pacman)- A theme with dynamic tagcloud and dynamic snow

一些基本路径

文章在source/_posts, 文章支持Markdown语法，可以使用一些MarkDown渲染工具。如果想修改头像可以直接在主题的_config.yml文件里面修改，友情链接，之类的都在这里。开始打理你的博客吧，有什么问题或者建议，都可以提出来，我会继续完善的。

### 修改头像

我当前的路径/Users/leopard/blog/themes/yilia，ls 你可以看到

**Gruntfile.js    _config.yml package.json**

**README.md  layout      source**

vim _config.yml 进去，找到 #你的头像url avatar: 后接一个URL就行了，头像就修改成功了

修改主题和作者名字

我当前的路径/Users/leopard/blog，ls 你可以看到

**_config.yml  node_modules    public      source      themes**

**db.json        package.json     scaffolds   ssh-keygen**

vim _config.yml 进去，找到 author: 潘柏信，修改成你自己的名字就行了

修改主题，然后继续往下找到

### Extensions

##### Plugins: http://hexo.io/plugins/

##### Themes: http://hexo.io/themes/

theme:yilia

改成theme: yilia，theme:后面接你自己的主题名字就行了,然后分别执行

部署 **hexo g**

提交 **hexo d**

你的主题，和名字就修改成功了

**Markdown语法参考链接**

[链接一](https://www.zybuluo.com/mdeditor)

---




