---
abbrlink: ''
categories:
- - 博客
- - 创建博客
date: '2023-10-10T19:22:39.707330+08:00'
description: 访问Qexo编辑页面即可在线编辑博客，换设备也无所谓了
tags:
- qexo
- 博客
- vercel
- githubpage
- 图床
title: Qexo实现Hexo博客在线编辑
updated: 2023-10-10T19:22:40.285+8:0
---
## 基本信息

| **服务器选择** | Qexo、Vercel、GitHub | **博客时间** | 2023.10.10 |
| --- | --- | --- | --- |
| **搭建结果** | 访问Qexo编辑页面即可在线编辑博客，换设备也无所谓了 |  |  |

## 一键部署到vercel平台

我是用的是Vercel 数据库，所以直接看文档部署就好
[vercel部署](https://www.oplog.cn/qexo/start/build.html)
完成之后即可访问Qexo编辑页面

## 配置

### 博客配置

根据[文档](https://www.oplog.cn/qexo/configs/provider.html)修改
效果如下

![img1.png](https://s2.loli.net/2023/10/10/zlyk6VnZcQHj9eK.png)
注意修改博客路径

### vercel配置

看[文档](https://www.oplog.cn/qexo/configs/vercel.html)就行

### 图床配置

smms图床设置如下

![img2.png](https://s2.loli.net/2023/10/10/kxfABJCzvSQRO2l.png)
github图床设置如下即可
![img3.png](https://s2.loli.net/2023/10/10/AsjwuRJcbp62VXe.png)

> 注意：github图床由于dns污染不挂代理没法访问，所以放弃使用这个

## 参考链接

[Qexo](https://www.oplog.cn/qexo/)
[SM.MS](https://smms.app/)
[静态博客管理器 qexo 搭建指南](https://youngjuning.js.org/c5e7f4e67be8/#%E9%83%A8%E7%BD%B2)



