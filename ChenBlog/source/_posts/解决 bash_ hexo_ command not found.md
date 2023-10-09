---
title: 解决"bash:hexo:command not found"
description: 解决报错
date: 2023-09-03 12:00:00
updated: 2023-09-03 12:00:00
tags: 
    - 博客
    - vercel
    - githubpage
    - 报错
categories: 
    - 博客
    - 日常问题
---

<a name="ILMNh"></a>
## 基本信息
| **问题描述** | 准备更新的时候输入`hexo s`，<br />报错`bash: hexo: command not found`                                                                     | **博客时间** | 2023.07.28 |
| --- |-------------------------------------------------------------------------------------------------------------------------------| --- | --- |
| **解决思路** | 本着能解决问题就不重装的原则，首先检查 nodes 和 npm 是否正常，依次输入命令 node -v 和 npm -v 看看是有相关版本信息。<br />出现了版本信息就证明 nodejs 和 npm 是没有问题的，那么就应该是环境变量的配置问题了 |  |  |

1. 在[此电脑] 右键[属性]，依次选择[高级系统设置]->[环境变量]，选择系统变量 Path，新建`C:\Users\zhc\OneDrive\Blog\ChenBlog\node_modules\.bin`，将 node modules 下的 bin 文件路添加到 Path 里面，
2. 注意你的博客目录下应该有两个 node modules 文件夹，例如我的是 E:BLOGlwww.itrhx.cominode modulesl.bin 和 E:BLOGWwww.itrhx.com\Hexolnode modulesl.bin
> **要添加到 Path 里面的是与 config.yml 在同一个目录的 node modules 文件夹!**

3. 环境变量添加好了之后重新打开 git 即可运行 hexo 命令，如果此时仍然无法执行 hexo 命令，那就只能拿出终极绝招了，运行命令 `npm install hexo-cli -g` 重新安装 hexo 即可!

