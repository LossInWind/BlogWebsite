---
abbrlink: ''
categories:
    - 博客
    - 创建博客
date: '2023-10-10T19:05:50.668291+08:00'
description: 使用GitHub Action实现Hexo博客自动发布
tags:
    - 博客
    - githubpage
    - GitHub Action
title: 使用GitHub Action实现Hexo博客自动发布
updated: 2023-10-10T19:47:2.2+8:0
---
## 基本信息


| **服务器选择** | GitHub                                | **博客时间** | 2023.10.10 |
| -------------- | ------------------------------------- | ------------ | ---------- |
| **搭建结果**   | 使用GitHub Action实现Hexo博客自动发布 |              |            |

## 准备

两个GitHub 仓库
一个作为博客源码的仓库：BlogWebsite，Private性质的
一个作为静态文件的仓库：lossinwind.github.io，即github pages的仓库
特别指出：由于我之前的工具集为本地架设，本地编译后push到lossinwind.github.io，结合云端的github pages发布的。所以lossinwind.github.io这个仓库已经有了，同时本地的hexo代码也已经有了。我是在此基础上进行的github actions自动发布的搭建

## git配置

开头我们已经创建了 BlogWebsite 的 github 仓库，我们将BlogWebsite与本地的hexo项目关联上，即先push到BlogWebsite仓库中去
进入本地目录：在本地项目右键gitbash here 或者  $ cd /Users/project/hexo
然后执行下面步骤

```git
$ git init
$ git add .
$ git commit -m”first commit”
$ git remote add origin 'https://github.com/LossInWind/BlogWebsite.git'
$ git push -f origin master
```

> origin为远程仓库名，可以随便设置。'[https://github.com/LossInWind/BlogWebsite.git](https://github.com/LossInWind/BlogWebsite.git)'为仓库的url地址

## 仓库配置

先使用git生成一对密钥，在用户/.ssh文件下生成hexo-deploy-key 和 hexo-deploy-key.pub 两个文件

### 首先

BlogWebsite仓库进行配置：Settings -> Secrets -> Actions -> new repository secret 页面上添加一个新的 Secret
添加如下

```
Name: HEXO_DEPLOY_PRI  # 这个值下面还会用到的
Value: hexo-deploy-key 中的私钥
```

### 其次

lossinwind.github.io仓库进行配置：Settings -> Deploy keys -> add deploy key 页面上添加一个新的 Secret。添加如下

```
Title: hexo-deploy-key
Key: 填入 hexo-deploy-key.pub 中的公钥内容
勾选 Allow write access 选项，允许写入权限
```

### 新建GitHub Action编写 Workflow

```yaml
name: Hexo Deploy
# 只监听 source 分支的改动
on:
  push:
    branches:
      - main

# 自定义环境变量，这个GIT_USER和GIT_EMAIL配置成你自己的，GIT_EMAIL尽量和上面的`ssh-keygen -f hexo-deploy-key -C "your email"`中的your email保持一致
env:
  GIT_USER: LossInWind
  GIT_EMAIL: 2984263497@qq.com

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./ChenBlog

    steps:
      # 获取博客源码和主题
      - name: Checkout
        uses: actions/checkout@v2

      # 这里用的是 Node.js 13.x，14.x 生成 Hexo 静态页面会有问题
      - name: Set up Node.js
        uses: actions/setup-node@v1
        with:
          node-version: '12'
        
      - name: 安装 Hexo CI
        run: 
          npm install hexo-cli -g --no-fund && npm install hexo-server --save --no-fund

      # 安装依赖
      - name: Install Dependencies
        run: |
          npm install --no-fund

      # 从之前设置的 secret 获取部署私钥
      - name: Setup SSH Keys and known_hosts
        env:
          HEXO_DEPLOY_PRI: ${{ secrets.HEXO_DEPLOY_PRI }} 
        run: |
          sudo timedatectl set-timezone "Asia/Shanghai"
          mkdir -p ~/.ssh
          echo "$HEXO_DEPLOY_PRI" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name $GIT_USER
          git config --global user.email $GIT_EMAIL

      # 生成并部署 `npx hexo clean && npx hexo g -d` or `npm run deploy`
      - name: Deploy
        run: |
          hexo clean && hexo g && hexo d
```

> 注意，内容中的 HEXO_DEPLOY_PRI 需要和上面BlogWebsite仓库的 Secrets的name保持一致。待编辑好保存

## 执行效果

当我们将本地的hexo项目push到BlogWebsite仓库后，在BlogWebsite仓库我们可以看到代码已经更新了，
然后，我们点击Actions tab，可以看到执行成功
此时编译渲染后的页面也会推送到lossinwind.github.io仓库，github page也会更新

## 可能出现的问题

### themes/butterfly目录代码未提交到仓库

actions发布过程没有报错。但是 Post Checkout 节点可以发现提示：fatal: No url found for submodule path 'themes/next' in .gitmodules
同时查看仓库文件，可以发现BlogWebsite 仓库的 themes/butterfly 路径下啥也没有，而本地hexo项目 themes/butterfly 下包含很多文件夹和文件
此时可以知道**问题**是themes/butterfly/ 文件夹下的文件夹及文件没有上传到 BlogWebsite 仓库。

#### 原因

因为  themes/butterfly/有自己的 .git，即  themes/butterfly/ 是 /Users/project/hexo 下 .git 的 submodule。
简单地说，就是外部仓库无法 track 一个嵌套仓库，外部仓库所有的 add，commit，push 都与嵌套仓库没有任何关系。

#### 解决方法

> 注意：如果你之前尝试添加过嵌套仓库，应该先执行以下命令移除它：git rm -f --cached themes/butterfly

最简单直接的方法就是把  themes/butterfly 目录下的 .git 文件夹删掉，这样我们的主题文件夹就是一个普通的目录，而不是一个 git 仓库，这样嵌套仓库的内容就会成为当前仓库内容的一部分，就可以使用 git add 对这些文件进行跟踪。
操作完以上，再在本地hexo项目根路径下进行 git add commit 等操作后，会发现上述问题都好了。

### hexo c && hexo g && hexo d执行不了

没有报错，但是lossinwind.github.io仓库没有更新，说明hexo d出现问题
同时显示

```
Usage: hexo <command> 

Commands: 
help Get help on a command. 
init Create a new Hexo folder. 
version Display version information. 

Global Options: 
--config Specify config file instead of using _config.yml 
--cwd Specify the CWD 
--debug Display all verbose messages in the terminal 
--draft Display draft posts 
--safe Disable all plugins and scripts 
--silent Hide output on console 

help, you can use 'hexo help [command]' for the detailed information 
or you can check the docs: http://hexo.io/docs/
```

#### 原因

因为hexo没有在正确的目录运行，可以通过运行pwd 或 ls来确定当前工作流的文件夹

#### 解决方法

添加表明当前工作目录的代码working-directory: ./ChenBlog

```yaml
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./ChenBlog
```

## 参考链接

[GitHub Actions实现Hexo博客自动发布](https://yaoyuanyy.github.io/2022/02/28/GitHub-Actions%E5%AE%9E%E7%8E%B0Hexo%E5%8D%9A%E5%AE%A2%E8%87%AA%E5%8A%A8%E5%8F%91%E5%B8%83/index.html#%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98)
[记录github action自动部署hexo butterfly](https://www.maya1900.top/createsite/0430/)
[Hexo 自动部署](https://www.oplog.cn/archives/24998.html)
[通過travis-ci或者GitHub Actions自動化部署GitHub Pages和Coding Pages](https://blog.crazywong.com/posts/74006f42/#%E5%89%B5%E5%BB%BAActions)
