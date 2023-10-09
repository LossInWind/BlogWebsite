---
title: 搭建博客(2)
description: 把github静态博客托管到vercel
date: 2023-07-27 12:00:00
updated: 2023-07-27 12:00:00
tags: 
    - 博客
    - vercel
    - githubpage
categories: 
    - 博客
    - 创建博客
---

<a name="ILMNh"></a>
## 基本信息
| **服务器选择** | vercel | **博客时间** | 2023.07.27 |
| --- | --- | --- | --- |
| **搭建结果** | 把github静态博客托管到vercel |  |  |

1. Vecel 可以直接用 Github 的账号登录
2. 在控制台创建一个新项目，如果没有在 Github 中没有安装 Vercel 应用，会弹出如下图所示的弹窗：
3. 点击需要与 Vercel 关联的账号与团队，会进入具体配置页面：
4. 可以选择绑定所有的仓库，也可以选择绑定部分仓库。
5. 安装完成后，就可以 import 对应的仓库了
6. 最后，点击 Deploy 就可以进行部署了
7. 此时 Vercel 会拉取远程仓库的代码并构建，最后部署到 Verccel 的页面服务器上。

Vercel 分配域名的规则是 <project name>[-<random word>].vercel.app 。<br />当 Github 远程仓库的对应分支上有 commit 时，则会触发 Vercel 的构建和部署。<br />在 Vercel 的项目设置中，可以更改远程仓库对应的分支，绑定自有域名和自定义构建命令等操作。
<a name="mTlRt"></a>

