---
title: szu校园网自动登录
description: 开机自启动，发送伪装请求头实现登录
date: 2023-08-04 00:00:00
updated: 2023-08-04 09:00:00
tags: 
    - szu
    - python
    - 抓包
categories: 
    - 技术杂谈
---
<a name="ILMNh"></a>
## 基本信息
| **服务器选择** | python打包exe | **博客时间** | 2023.08.04 |
| --- | --- | --- | --- |
| **搭建结果** | 开机自启动，发送伪装请求头实现登录 |  |  |

>只想拿来就用，可以跳到最后，请给我GitHub一个star或者一个follow~
<a name="XItAT"></a>
## 分析登录过程
F12查看登录校园网过程<br />登录校园网的请求如下：<br />请求URL：http://172.30.255.42:801/eportal/portal/login?callback=dr1003&login_method=1&user_account=用户名&user_password=密码&wlan_user_ip=172.30.66.101&wlan_user_ipv6=&wlan_user_mac=000000000000&wlan_ac_ip=172.30.255.41&wlan_ac_name=&jsVersion=4.1.3&terminal_type=1&lang=zh-cn&v=2573&lang=zh<br />请求方法：GET<br />请求头部信息：
```http
Accept: /
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Connection: keep-alive
DNT: 1
Host: 172.30.255.42:801
Referer: http://172.30.255.42/
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.188
```
查询字符串参数：
```http
callback=dr1003
login_method=1
user_account=用户账号
user_password=密码
wlan_user_ip=172.30.66.101
wlan_user_ipv6=
wlan_user_mac=000000000000
wlan_ac_ip=172.30.255.41
wlan_ac_name=
jsVersion=4.1.3
terminal_type=1
lang=zh-cn
v=2573
lang=zh
```
这个请求包含用户账号、密码以及其他一些参数，是用于登录校园网的请求。可以使用Python的requests库或其他HTTP客户端库来发送这个GET请求，模拟登录校园网。
<a name="pam1D"></a>
## python实现
```python
import requests
import socket
import json
def get_local_ip():
    try:
        # 获取本地主机名
        host_name = socket.gethostname()

        # 获取本地IP地址
        local_ip = socket.gethostbyname(host_name)
        return local_ip
    except socket.error as e:
        print("获取本地IP地址失败:", e)
        return None

def is_internet_connected():
    try:
        # 使用百度作为一个简单的测试URL
        response = requests.get("http://www.baidu.com", timeout=5)
        return True if response.status_code == 200 else False
    except requests.exceptions.RequestException:
        return False

def login_campus(username, password):
    # 获取本地IP地址
    local_ip = get_local_ip()
    if not local_ip:
        print("无法获取本地IP地址。登录失败。")
        return

    # 登录校园网的URL
    login_url = "http://172.30.255.42:801/eportal/portal/login"
    headers = {
        "Accept": "*/*",
        "Accept-Encoding": "gzip, deflate",
        "Accept-Language": "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",
        "Connection": "keep-alive",
        "DNT": "1",
        "Host": "172.30.255.42:801",  # 请求的目标主机
        "Referer": "http://172.30.255.42/",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36 Edg/115.0.1901.188"
    }

    # 构建GET请求的参数
    params = {
        "callback": "dr1003",
        "login_method": "1",
        "user_account": username,
        "user_password": password,
        "wlan_user_ip": local_ip,  # 使用获取到的本地IP地址作为参数
        "wlan_user_ipv6": "",
        "wlan_user_mac": "000000000000",
        "wlan_ac_ip": "172.30.255.41",
        "wlan_ac_name": "",
        "jsVersion": "4.1.3",
        "terminal_type": "1",
        "lang": "zh-cn",
        "v": "2573",
    }

    try:
        # 发送GET请求来登录
        response = requests.get(login_url, headers=headers, params=params)

        #print(response.text)

        # 修改登录成功的判断逻辑
        if "result" in response.text and "msg" in response.text:
            json_response = response.text.split("(", 1)[1].rsplit(")", 1)[0]
            data = json.loads(json_response)

            if data["result"] == 1 and data["msg"] == "Portal协议认证成功！":
                print("登录成功！")
                if is_internet_connected():
                    print("已成功联网！")
                else:
                    print("未能联网。")
            elif data["result"] == 0 and data["msg"] == "IP: 172.29.20.95 已经在线！" and data["ret_code"] == 2:
                print("已经在线")
            else:
                print("登录失败，请检查用户名和密码。")
        else:
            print("登录失败，请检查用户名和密码。")
    except requests.exceptions.RequestException as e:
        print("登录失败:", e)


if __name__ == "__main__":
    # 填写你的用户名和密码
    username = "用户名"
    password = "密码"

    # 调用登录函数
    login_campus(username, password)

    # Pause the execution to keep the console window open after the .exe finishes running
    input("Press Enter to exit...")
```
<a name="XH33X"></a>
## 打包exe
使用pyinstaller，从Python脚本创建独立的可执行文件。以下是操作：
<a name="IDkC1"></a>
### 安装PyInstaller:
打开命令提示符或终端，然后使用pip安装pyinstaller：
```python
pip install pyinstaller
```

<a name="b8Mhv"></a>
### 创建exe
cd进入到Python脚本所在的目录。<br />使用pyinstaller创建可执行文件：
```python
pyinstaller --onefile your_script_name.py
```
将_script_name.py替换为Python脚本文件的名称（例如campus_login.py）。<br />--onefile选项告诉PyInstaller将所有内容捆绑到一个.exe文件中。<br />运行上述命令后，PyInstaller将在与脚本相同的目录中创建一个新的dist文件夹。在dist文件夹中，会发现与脚本同名的独立可执行文件（例如，your_script_name.exe）。
<a name="yxuwO"></a>
## 设置开机自启动

1. win+R 启动运行界面
2. 输入：`shell:startup`

![image.png](https://raw.githubusercontent.com/LossInWind/giscus_talk/main/szu01.png)

3. 把快捷启动项放入启动文件夹

![image.png](https://raw.githubusercontent.com/LossInWind/giscus_talk/main/szu02.png)


之后就可以开机启动啦
## 安装包使用教程

1. 点击[链接](https://github.com/LossInWind/szu_autolog)进入GitHub仓库，找到右侧release!

![image.png](https://raw.githubusercontent.com/LossInWind/giscus_talk/main/szu03.png)

2. 下载exe文件
3. 设置开机自启动（看前文）
> 注意 : 第一次使用需要点击运行一下输入用户密码;以后如果想换账号或者输错密码需要修改，删掉同一文件夹下的config.ini文件即可

> 最后球球大家给我的项目一个star吧0.0~ 或者一个follow~

<a name="mTlRt"></a>
## 参考链接
没有参考！独立创作！