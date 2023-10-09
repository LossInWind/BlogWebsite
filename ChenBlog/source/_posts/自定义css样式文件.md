---
title: 自定义css样式文件
description: 添加自定义css样式文件的方法
date: 2023-08-01 00:00:00
updated: 2023-08-01 00:00:00
tags: 
    - css
    - 博客
categories: 
    - 博客
    - 博客美化  
---

<a name="ILMNh"></a>
## 基本信息
| **服务器选择** | Hexo+Butterfly主题 | **博客时间** | 2023.08.01 |
| --- | --- | --- | --- |
| **搭建结果** | 添加自定义css样式文件的方法 |  |  |

:::info
原则是尽量不直接修改了主题的文件，这样在更新主题时不会出问题（除非不会再更新主题）
:::
<a name="XItAT"></a>
## 创建文件夹
在你blog主目录下的source目录下(一定不要是themes目录下那个，那个是主题的source文件)分别创建好css、js、img文件夹，主要是用来存放你自己创建的css、js文件以及图片；这三个文件夹是基础需要的，还有其他的配置文件可以自己另行创建文件夹<br />这里把这些自定义的配置文件都归档到了source/config下了<br />
![image.png](https://raw.githubusercontent.com/LossInWind/giscus_talk/main/202308011220286.png)
<a name="WwhBR"></a>
## 引入自定义的js、css文件
直接在主题配置文件中配置即可，我的主题配置文件为上图的_config.butterfly.yml<br />配置方法如下：在主题中找到配置inject:
```yaml
# Inject
# ps：引入自定义css、js
#     css文件在head引入，js文件在bottom里引入，有的js要求在head引入，不然无法生效
# Insert the code to head (before '</head>' tag) and the bottom (before '</body>' tag)
# ps: 插入代码到头部 </head> 之前 和 底部 </body> 之前
inject:
  head: #注入自定义css
      - <link rel="stylesheet" href="/css/style.css?v1">
    # - <link rel="stylesheet" href="/xxx.css">
  bottom: #注入自定义js
      - <script src="/js/script.js?v1"></script>
    # - <script src="/xxxx"></script>
```
> ⚠️注意路径一定是/xx/xx，不能是./xx/xx

<a name="mTlRt"></a>
## 参考链接
[Hexo博客添加自定义css和js文件Hexo博客添加自定义css和js文件](https://blog.leonus.cn/2022/custom.html)
