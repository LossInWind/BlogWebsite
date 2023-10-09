---
title: Readme
date: 2023-07-20 00:00:00
updated: 2023-07-20 00:00:00
top: 5
tags: "博客"
---

<a name="k4Wxa"></a>
## 博客介绍
1. 原理：在本地使用Nodejs框架写好博客，使用hexo生成网页，最后使用git上传至github分支，Vercel检测到分支变动自动部署，最后使用域名到域名的转发（Cname）实现博客访问
2. 部署服务器：Github Page+Vercel
3. 域名：腾讯云域名
4. 博客文件保存地址：OneDrive

博客文件结构介绍：--Blog<br />--ChenBlog(这个文件夹里面是Hexo根目录)<br />--source（博客文件）<br />--_data<br />--link.yml（link页面内容）<br />--_posts（博客文章）<br />--link（link链接页面设置）<br />--tags（tags标签页面设置）<br />--themes（主题）<br />--butterfly（butterfly主题）<br />--source<br />--img（存放博客所需图片）<br />!![image](https://github.com/LossInWind/lossinwind.github.io/assets/80253340/6d3c7d02-3dac-4a19-94d2-52b83ad47326)

<a name="ClA0H"></a>
## 博客使用
<a name="Dw1s0"></a>
### 在新电脑上使用时

- 安装nodejs和git
- 在OneDrive文件夹的Hexo根目录下，右键git bash

设置用户名：`$ git config --global user.LossInWind`github用户名<br />设置邮箱： `$ git config --global user.2984263497@qq.com`github邮箱

- 创建SSH Key
> 一般来说一台电脑只需要生成一次 SSH Key 就行了，不同的 Git 远程仓库都添加这同一个 SSH Key 也就行了。
- 打开Git Bash，创建SSH Key：

`$ ssh-keygen -t rsa -C "2984263497@qq.com"`

- 然后可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
- 登陆GitHub，打开“settings”，“SSH and GPG Keys”页面，点击‘New SSH key’，然后填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容，然后点击’Add SSH key’即可
- git即链接完成，此后只需在在OneDrive文件夹的Hexo根目录下，右键git bash即可
<a name="OCDoI"></a>
### 发布新博客时
使用md文件直接放进_posts文件夹下即可<br />文件头如下
```yaml
---
title:
date:
updated:
tags:
categories:
keywords:
description:
top_img:
comments:
cover:
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
mathjax:
katex:
aplayer:
highlight_shrink:
aside:
abcjs:
---
```
<a name="lNZsY"></a>
### 设置文章排序优先级
<a name="YGCj6"></a>
#### 安装插件
```yaml
npm uninstall hexo-generator-index --save
npm install hexo-generator-index-pin-top --save
```
<a name="qRiJM"></a>
#### 配置文章<br />然后在需要置顶的文章的Front-matter中加上top选项即可<br />top后面的数字越大，优先级越高
```yaml
---  
title: 2019  
date: 2019-02-14 16:10:03  
top: 5
---
```
<a name="lXsCz"></a>
### 使用giscus评论系统
<a name="oA22v"></a>
#### **1、创建新仓库**
<a name="cmK6L"></a>
#### **2、安装giscus**
点击这个链接安装：[GitHub Apps - giscus](https://link.zhihu.com/?target=https%3A//github.com/apps/giscus)<br />选择刚建立的仓库，点击install
<a name="grzba"></a>
#### **3、配置仓库discuss部分**
首先打开仓库的setting，将Discussions部分打上对号<br />然后建立一个Announcements的分类（默认存在）
<a name="Vg9Xx"></a>
#### **4、进入giscus配置**
链接在这：[giscus](https://link.zhihu.com/?target=https%3A//giscus.app/zh-CN)<br />配置完上述部分后，可以在”启动giscus“下边复制几个东西
```yaml
giscus:
  repo: "LossInWind/giscus_talk"
  repo_id: "R_kgDOKAu7tg"
  category_id: "DIC_kwDOKAu7ts4CYMGx"
```
注意勾选：
Discussion 的標題包含頁面的網址
giscus 將尋找標題中包含頁面網址的 discussion

然后记得改
```yaml
comments:
  # Up to two comments system, the first will be shown as default
  # Choose: Disqus/Disqusjs/Livere/Gitalk/Valine/Waline/Utterances/Facebook Comments/Twikoo/Giscus/Remark42/Artalk
  use: Giscus # Valine,Disqus
```
<a name="EeP6d"></a>
#### **5、大功告成**
试了几次才成功，之前一直抽风显示repo_id和category_id一样

注意格式不同

最后博客文章部分就有了下边的评论区
<a name="ppUEg"></a>
## 问题总结
安装时出现<br />33 packages are looking for funding<br />  	run `npm fund` for details<br />命令后接`--no-fund`即可