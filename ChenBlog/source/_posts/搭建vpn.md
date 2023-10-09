---
title: 搭建VPN
description: 使用icrosoft Azure搭建VPN
date: 2023-07-28 00:00:00
updated: 2023-07-28 00:00:00
tags: "服务器VPS"
categories: 
    - VPS服务器
    
---

<a name="ILMNh"></a>
## 基本信息
| **服务器选择** | Microsoft Azure | **博客时间** | 2023.07.28 |
| --- | --- | --- | --- |
| **搭建结果** | 使用指定域名即可访问服务器指定端口，即跳转到vpn管理面板，使用管理面板管理代理节点即可 |  |  |

<a name="mnNTD"></a>
## 搭建VPN
<a name="YilGI"></a>
### 1.设置ip并开放端口

- **设置ip地址：**拿到服务器之后首先设置ip地址为静态
- **开放端口**：一般服务器都可以通过设置安全组来开放指定端口。

添加入站出站端口规则，这里为了省事直接在端口范围填的*，表示所有端口都开放（非常不安全！）<br />![image.png](img/搭建VPN/1.png)
<a name="KBvnw"></a>
### 2.ssh连接服务器并启用管理员权限

- 服务器用户名：  密码：
- 命令行输入： 输入密码： 启用管理员权限
<a name="ySxrr"></a>
### 3.安装x-ui，添加代理

- **安装：**使用ssh命令行界面安装：
```
wget -N https://gitlab.com/rwkgyg/x-ui-yg/raw/main/install.sh && bash install.sh
```

- **设置：**设置代理管理网站的用户名密码
- 使用订阅转换工具[https://acl4ssr-sub.github.io/](https://acl4ssr-sub.github.io/)转换二维码和订阅链接
<a name="lgqYM"></a>
## nginx反向代理
> 仍然需要进入管理面板需要添加ip地址加端口号，即[http://20.231.210.160:54322/](http://20.231.210.160:54322/)才能进入
> 下面使用nginx进行反向代理，通过端口监听将域名跳转至服务器指定端口

<a name="KKXGf"></a>
### 安装nginx（Ubuntu系统）

- 首先使用`nginx -v`查看nginx是否已经被安装
- 使用`apt-get`安装，如果你的系统不是Ubuntu，可以查看网上其他安装教程。输入`sudo apt-get install nginx`
- 输入你的root密码
- 使用`service nginx start`启动nginx服务。
> 这时访问你的[服务器](https://activity.huaweicloud.com/discount_area_v5/index.html?utm_source=hwc-csdn&utm_medium=share-op&utm_campaign=&utm_content=&utm_term=&utm_adplace=AdPlace070851)公网ip，就可以查看Nginx的默认欢迎页了

<a name="Jx3YS"></a>
### 配置Nginx

- 首先打开文件夹`/etc/nginx`,查看`nginx.conf`中，`http{}`里面有没有`include /etc/nginx/conf.d/*.conf;`这一行，如果有，http会包含`conf.d`文件夹里面后缀为conf的配置文件，如果没有，就加上。（你在这步可以使用vim等工具，我用的vscode）
- 然后在`conf.d`文件夹中，创建一个文件，文件名字任意，后面加上.conf就行，我创建的`tama.conf`
- 然后修改`tama.conf`配置文件即可，因为这里我需要将域名转发到本地端口，所以配置如下
```
server{
	listen 80;
	server_name  www.vpn.haichen.work;
	index index.php index.html index.htm;

  location / {
    proxy_pass  http://20.231.210.160:54322/; # 转发规则
    proxy_set_header Host $proxy_host; # 修改转发请求头，让8080端口的应用可以受到真实的请求
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

> **注意**：如果你使用的云服务器，记得添加防火墙规则，放开你的指定端口


- 修改完成配置之后，记得先使用`nginx -t`查看配置文件写的是否正确，如果报错，请仔细检查文件。

> **注意**：之前一直报错是因为只需要在/etc/nginx/nginx.conf配置文件中的http{}函数里面有include /etc/nginx/conf.d/*.conf;这一行即可，其他地方不要有，注意是http{}函数里面


- 显示test is successful之后，输入`nginx -s reload `重新载入配置文件。
- 此时即可通过域名访问服务器指定端口

<a name="aERrl"></a>
### 补充
下面再附上两个命令（修改配置文件后reload就行，没有必要重启）<br />`nginx -s reopen      # 重启 Nginx`<br />`nginx -s stop        # 停止 Nginx`
<a name="mTlRt"></a>
## 参考链接

- [手把手教你使用Ubuntu安装Nginx并进行网站部署](https://huaweicloud.csdn.net/635638b9d3efff3090b5b0fb.html?spm=1001.2101.3001.6650.5&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Eactivity-5-125805069-blog-119998842.235%5Ev38%5Epc_relevant_sort_base2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Eactivity-5-125805069-blog-119998842.235%5Ev38%5Epc_relevant_sort_base2&utm_relevant_index=8)
- [用Nginx做端口转发（反向代理）配置文件的格式教学](https://zhuanlan.zhihu.com/p/108740468)
- [x-ui修改版一键脚本（xray内核）](https://github.com/yonggekkk/x-ui-yg)
- [订阅转换链接](https://github.com/acl4ssr-sub/acl4ssr-sub.github.io)
