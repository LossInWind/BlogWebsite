---
title: 搭建博客(1)
description: 搭建github静态博客
date: 2023-07-27 00:00:00
updated: 2023-07-27 00:00:00
tags: 
    - 博客
    - githubpage
categories: 
    - 博客
    - 创建博客
---

<a name="ILMNh"></a>
## 基本信息
| **服务器选择** | github page | **博客时间** | 2023.07.27 |
| --- | --- | --- | --- |
| **搭建结果** | 使用github page搭建博客，最终可以使用github的地址访问网站 |  |  |

<a name="XItAT"></a>
### 本地创建Hexo+Butterfly
<a name="AqLDT"></a>
#### 安装Node.js和git
<a name="UTIHR"></a>
#### 安装hexo
<a name="kmCxs"></a>
#### 安装butterfly主题
<a name="FgoeE"></a>
### Git+GitHub管理仓库
<a name="pyiCU"></a>
#### GitHub创建项目
GitHub上新创建一个用户名.github.io仓库，项目格式名称为[用户名].github.io，如下：<br />`username.github.io`
<a name="SAfsp"></a>
#### 安装git
<a name="mu1Kj"></a>
#### 设置git使其与远程仓库建立链接
<a name="ctpzk"></a>
##### 先设置git提交时的用户名和邮箱（初次一定要设置）
git设置用户名（github用户名）和邮箱（github邮箱）：（注意空格）
> 全局设置（对所有git工程都有效）
> 设置用户名：`$ git config --global user.name` 用户名
> 设置邮箱： `$ git config --global user.email` 邮箱

> 对特定工程设置（要在命令行中切换到特定工程目录下执行）
> 设置用户名：`$ git config user.name` 用户名
> 设置邮箱：`$ git config user.email` 邮箱

git 修改用户名和邮箱：
> 修改当前project
> 用户名的命令为：`$ git config user.name` 你的目标用户名;
> 提交邮箱命令为：`$ git config user.email` 你的目标邮箱名;
> git修改全局项目
> `$ git config --global user.name` 你的目标用户名；
> `$ git config --global user.email` 你的目标邮箱名;

<a name="ATCTJ"></a>
##### 创建SSH Key
> <a name="d9W4E"></a>
##### 一般来说一台电脑只需要生成一次 SSH Key 就行了，不同的 Git 远程仓库都添加这同一个 SSH Key 也就行了。

- 打开Git Bash，创建SSH Key：

`$ ssh-keygen -t rsa -C "youremail@example.com"`

- 然后可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。
- 登陆GitHub，打开“settings”，“SSH and GPG Keys”页面，点击‘New SSH key’，然后填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容，然后点击’Add SSH key’即可
<a name="rZSJ8"></a>
##### Git仓库和本地工程连接
配置hexoconfig即可略过
<a name="atXyb"></a>
##### 设置hexoconfig
打开_config.yml文件，设置deployment如下
```yaml
deploy:
  type: git 
  repo: git@github.com:LossInWind/lossinwind.github.io.git //ssh地址
  branch: master //部署分支
```
:::info
注意这里的分支，要和github中的分支保持一致，同时还要和github的page设置中的分支保持一致
:::
<a name="YilGI"></a>
### 到这里就可以使用github的地址访问网站了，如果想要使用域名来访问网站，请看下一篇博客
<a name="mTlRt"></a>
## 参考链接
[使用SSH建立Git 远程仓库和本地库连接](https://blog.csdn.net/chenxi_li/article/details/93380649)

