---
title: hexo 主题使用
date: 2017-07-15 15:23:53
tags: hexo
---


## 更换为`next`主题

进入hexo站点文件夹

```
cd your-hexo-site

```

clone `Next`代码并存放到 `themes/next` 目录下（用目前比较火的next主题为例）

```
git clone https://github.com/iissnan/hexo-theme-next themes/next

```

在站点 `_config.yml` 中修改:

```
theme: next

```

<!-- more -->

`Next` 主题拥有”题中题”，可以在主题的 `_config.yml` 中设置：

```
scheme: Mist  # 开启Next.Mist主题

```

## <a name="t2" target="_blank"></a>设置头像

在站点 `_config.yml` 中添加

```
avatar: http://....  # 头像的URL

```

## <a name="t3" target="_blank"></a>添加分类

新建一个页面，命名为 `categories`

```
hexo new page categories

```

在 `source/categories` 目录的 `index.md` 中修改:

```
title: 分类
date: 2015-12-02 12:44:45
type: "categories"
---

```

在主题的 `_config.yml` 中取消注释:

```
categories: /categories

```

在要分类的文章中加入 `category` 属性:

```
category: "hexo"  # 分类为 hexo

```

## <a name="t4" target="_blank"></a>添加标签

新建一个页面，命名为 `tags`

```
hexo new page tags

```

在 `source/tags` 目录的 `index.md` 中修改:

```
title: tags
date: 2015-12-02 12:10:34
type: "tags"
---

```

在主题的 `_config.yml` 中取消注释:

```
tags: /tags

```

在需要变迁的文章中加入 `tgs` 属性:

```
tags: [spark,sparkSQL]  # 添加spark和sparkSQL两个标签

```

## <a name="t5" target="_blank"></a>添加评论

在 [多说](http://duoshuo.com) 创建一个站点，创建时填写的多说域名就是你的 `duoshuo_shortname`

在站点 `_config.yml` 中设置：

```
duoshuo_shortname: your_duoshuo_shortname

```

## <a name="t6" target="_blank"></a>更多功能

参考 [next官方帮助文档](http://theme-next.iissnan.com)
