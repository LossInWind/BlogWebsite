---
abbrlink: ''
categories:
- - 技术杂谈
date: '2023-10-12T16:05:43.302455+08:00'
description: 分析szu统一身份认证登录过程
tags:
- szu
- python
- CAS
- 抓包
title: szu统一身份认证界面解析
updated: 2023-10-12T16:5:46.167+8:0
---
## 基本信息

| **语言/知识点** | cas、python              | **博客时间** | 2023.10.12 |
| --------------- | ------------------------ | ------------ | ---------- |
| **结果**        | 分析统一身份认证登录过程 |              |            |

## 背景

每次校园网内登录办事大厅或者在浏览IEEE期刊时，都会要求验证学生身份，此时都是使用深圳大学统一身份认证来进行学生认证。
![1.png](https://s2.loli.net/2023/10/12/npCPM18Fg5TtBVz.png)
这篇文章将会分析这个过程并尝试使用python发包登录

## 网络包分析登录过程

F12查看身份认证过程
可以看到身份认证的请求按时间轴排布如下：
![2.png](https://s2.loli.net/2023/10/12/MnCgIvGw6tbQaNr.png)
分析这个过程我们可以知道这是一个CAS统一认证登录的过程，简单来说，过程如下：
第1步，浏览器正常发起Web服务请求（带过期cookie或者无cookie）
第2步，web服务发现cookie无效，重定向请求到CAS登录界面进行认证
第3步，用户填写认证信息后，将认证信息POST给CAS服务器
第4步，CAS服务器判定认证信息有效，生成一个Cas-cookie，并返回一个一次性有效ticket
第5步，浏览器存储cas-cookie，并带上ticket再次访问web服务
第6步，web服务会请求CAS服务器以验证ticket有效
第7步，CAS验证ticket有效后CAS即删除ticket，返回结果给web服务
第8步，web服务生成一个service-cookie，并返回web资源给浏览器

![3.png](https://s2.loli.net/2023/10/12/tLOP2lqnmYCEKDk.png)
具体知识可以参考：[实现内网CAS统一认证登录](https://blog.csdn.net/zhutou_xu/article/details/114212377)

### 第一个请求

首次访问web服务（GET一个校内url）时返回了一个302状态（即重定向），重定向的地址在响应标头的Location域中指出。查看location的值，可以确认重定向到了cas服务的登录界面，且带了一个service参数，指明了浏览器原访问的服务站点（为了cas认证后正确跳转回去）

```python
response = self.svr_session.get(url=serviceUrl, allow_redirects=False)
self.cas_url = response.headers["location"]
self.cas_url = self.cas_url1 + self.cas_url
print(self.cas_url)
```

此时cas_url为重定向到cas服务登录界面的url

### 第二个请求（认证页面的第一个请求）

#### 标头数据如下：

请求URL：[https://authserver.szu.edu.cn/authserver/login?service=https%3A%2F%2Fehall.szu.edu.cn%3A443%2Flogin%3Fservice%3Dhttps%3A%2F%2Fehall.szu.edu.cn%2Fnew%2Findex.html](https://authserver.szu.edu.cn/authserver/login?service=https%3A%2F%2Fehall.szu.edu.cn%3A443%2Flogin%3Fservice%3Dhttps%3A%2F%2Fehall.szu.edu.cn%2Fnew%2Findex.html)
请求方法：POST
请求标头：

```http
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding:gzip, deflate, br
Accept-Language:zh-CN,zh;q=0.9
Cache-Control:max-age=0
Connection:keep-alive
Content-Length:273
Content-Type:application/x-www-form-urlencoded
Cookie:route=091bbd9a6c8e2a18c423b0accfefdcd7; JSESSIONID_auth=P-MiqZzuK3EmeGNt8MmBVV0NOCddkqhnKX55SpEZDcJcFD4isXnf!778334097; org.springframework.web.servlet.i18n.CookieLocaleResolver.LOCALE=zh_CN
Dnt:1
Host:authserver.szu.edu.cn
Origin:https://authserver.szu.edu.cn
Referer:https://authserver.szu.edu.cn/authserver/login?service=https%3A%2F%2Fehall.szu.edu.cn%3A443%2Flogin%3Fservice%3Dhttps%3A%2F%2Fehall.szu.edu.cn%2Fnew%2Findex.html
Sec-Ch-Ua:"Microsoft Edge";v="117", "Not;A=Brand";v="8", "Chromium";v="117"
Sec-Ch-Ua-Mobile:?0
Sec-Ch-Ua-Platform:"Windows"
Sec-Fetch-Dest:document
Sec-Fetch-Mode:navigate
Sec-Fetch-Site:same-origin
Sec-Fetch-User:?1
Upgrade-Insecure-Requests:1
User-Agent:Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/117.0.0.0 Safari/537.36 Edg/117.0.2045.60
```

#### 负载部分如下：

查询字符串参数：

```http
service: https://ehall.szu.edu.cn:443/login?service=https://ehall.szu.edu.cn/new/index.html
```

表单数据：

```http
username: 2210433109
password: MukNAJb1ti4x93ujn0jcK5KaxdVSjsoO8ksDoyRvtUDqIfvTvxcdmm3kTYH5NCxZY1VMsD3mbddh2ZQ2BDXSK24LAjsfOeEll6cwQU5aEIc=
lt: LT-412239-fBpb6E51MWcxpQvfsTwcrdM7jGsqA31697093632120-SjdO-cas
dllt: userNamePasswordLogin
execution: e2s1
_eventId: submit
rmShown: 1
```

#### 响应标头如下：

```http
Cache-Control:no-cache
Cache-Control:no-store
Connection:keep-alive
Content-Security-Policy:upgrade-insecure-requests;connect-src *
Date:Thu, 12 Oct 2023 07:01:24 GMT
Expires:Thu, 01 Jan 1970 00:00:00 GMT
Location:https://ehall.szu.edu.cn:443/login?service=https://ehall.szu.edu.cn/new/index.html&ticket=ST-68862-useghL7Xv2VNpNRKubht1697094085223-sYAt-cas
Pragma:no-cache
Server:openresty
Set-Cookie:CASPRIVACY=; expires=Thu, 01-Jan-1970 01:00:00 GMT; path=/authserver/
Set-Cookie:CASTGC=TGT-21173-LbpXoJ5abPpFLNROzTfZB6RZQMJbXJJyYhtTOB6bM16wz4oCeK1697094085206-NYTe-cas;Path=/authserver/;HttpOnly;secure;
Set-Cookie:iPlanetDirectoryPro=9pRNN5LxbxDRw7FXc3N6w6; domain=.nju.edu.cn; path=/
Transfer-Encoding:chunked
```

#### 分析：

可以看到发送了一个POST请求，请求正文里存在用户名，密码。然而除了用户名密码外，还有几个不知道哪里来的字段_eventId、execution、lt、dllt、rmShown
一般来说，这些都属于登录表单中的隐藏字段，服务器会校验POST请求中这些字段必须和登录界面的一致，来避免暴力破解（作用和验证码有点类似）。我们在登录界面元素中用关键字搜索，果然可以找到。编码的时候我们可以用XPATH语句获取到对应的值。
![4.png](https://s2.loli.net/2023/10/12/mTGoMbdKwONHJ3u.png)
可以发现如果CAS认证成功，则会返回一个重定向地址（location）且带了参数（ticket）。我们后续第二次请求就访问这个地址（带上ticket），网站会向CAS服务验证这个ticket是否有效。
同时我们注意到发送了一个POST请求，请求正文里的密码是经过加密之后的结果，所以我们还需要找到网站是如何进行密码加密的。
加密方式肯定在源代码里，而且一般是通过js来实现的，所以我们查看源代码，可以找到名为encrypt.js
![5.png](https://s2.loli.net/2023/10/12/DWNhwoPc3rxzKe1.png)
查看js代码，可以找到最后有名字与encrypt相关的函数

```javascript
function getAesString(data, key0, iv0) {
  key0 = key0.replace(/(^\s+)|(\s+$)/g, "");
  var key = CryptoJS.enc.Utf8.parse(key0);
  var iv = CryptoJS.enc.Utf8.parse(iv0);
  var encrypted = CryptoJS.AES.encrypt(data, key, {
    iv: iv,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7
  });
  return encrypted.toString();
}
function encryptAES(data, aesKey) {
  if (!aesKey) {
    return data;
  }
  var encrypted = getAesString(
    randomString(64) + data,
    aesKey,
    randomString(16)
  );
  return encrypted;
}
function encryptPassword(pwd0, key) {
  try {
    return encryptAES(pwd0, key);
  } catch (e) {}
  return pwd0;
}
var $aes_chars = "ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678";
var aes_chars_len = $aes_chars.length;
function randomString(len) {
  var retStr = "";
  for (i = 0; i < len; i++) {
    retStr += $aes_chars.charAt(Math.floor(Math.random() * aes_chars_len));
  }
  return retStr;
}
```

把代码的其他部分注释掉可以发现并不影响函数，所以我们只需要搞明白这段代码的逻辑，然后在pyhton中复现即可。
同时发现这段代码的输入，除了密码pwd0之外，还有key，有了之前的经验，可以想到这个key肯定也属于属于登录表单中的隐藏字段，果然发现了名为pwdDefaultEncryptSalt的元素，这个就是我们想要的key了
![6.png](https://s2.loli.net/2023/10/12/LAHkVudob2nJjay.png)

#### 编码实现：

加密部分：

```python
from Crypto.Cipher import AES
import binascii
import random

def get_aes_string(data, key0, iv0):
    key0 = key0.strip()
    key = key0.encode('utf-8')
    iv = iv0.encode('utf-8')
    cipher = AES.new(key, AES.MODE_CBC, iv)
    data = data.encode('utf-8')
    # PKCS7 padding
    padding = 16 - (len(data) % 16)
    data += bytes([padding]) * padding
    encrypted = cipher.encrypt(data)
    return binascii.b2a_base64(encrypted).decode('utf-8')

def encrypt_aes(data, aes_key):
    if not aes_key:
        return data
    random_str = ''.join(random.choice("ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678") for _ in range(64))
    combined_data = random_str + data
    iv = ''.join(random.choice("ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678") for _ in range(16))
    encrypted = get_aes_string(combined_data, aes_key, iv)
    return encrypted

def encrypt_password(pwd0, key):
    try:
        return encrypt_aes(pwd0, key)
    except Exception as e:
        pass
    return pwd0

# #Example usage
# aes_chars = "ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678"
# password = "12345678"
# aes_key = "P3jwGjbOCIeZIxJc"

# encrypted_password = encrypt_password(password, aes_key)
# print("Encrypted Password:", encrypted_password)
```

登录部分：

```python
import requests
from lxml import etree
import re
import os
import pickle
import pyscript
from http.cookies import SimpleCookie
from encrypt import encrypt_password
from requests.cookies import create_cookie

class casService(object):
    def __init__(self, svr_session):
        self.cas_url1 = "https://authserver.szu.edu.cn/authserver"
        self.svr_session = svr_session  # service_session
        self.session = requests.session()  ## cas session
        #self.load_cascookies_from_file()  # 使用已有的cas-cookie(如果有的话)
        self.headers = {"Accept": "text/html, application/xhtml+xml, application/xml; q=0.9, /; q=0.8",
                        "Accept-Language": "zh_CN", "Connection": "keep-alive",
                        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36 Edge/18.18363", }

    def Login(self, serviceUrl="", username=None, password=None):
        response = self.svr_session.get(url=serviceUrl, allow_redirects=False)
        if response.status_code == 200:
            return True
        self.cas_url = response.headers["location"]
        self.cas_url = self.cas_url1 + self.cas_url
        print(self.cas_url)
        cas_response = self.session.get(self.cas_url, allow_redirects=False)
        if cas_response.status_code == 200:  # 登录界面
            if username == None or password == None:
                print("cas_cookie not valid")
                username = input("plase input username:")
                password = input("plase input password:")
            loginhtml = etree.HTML(cas_response.text)
            # 保存获得的loginhtml
            with open("loginhtml.html", 'w', encoding='utf-8') as f:
                f.write(cas_response.text)

            lt_value = loginhtml.xpath(
                "//input[@name='lt']/@value")  # LT-124388-0rAqHCMu3pTxpQoZ2J3OjBSlVv56fj1697029024655-SjdO-cas
            dllt_value = loginhtml.xpath("//input[@name='dllt']/@value")  # userNamePasswordLogin
            execution_value = loginhtml.xpath("//input[@name='execution']/@value")  # e1s1
            _eventId_value = loginhtml.xpath("//input[@name='_eventId']/@value")  # submit
            rmShown_value = loginhtml.xpath("//input[@name='rmShown']/@value")  # 1
            pwdDefaultEncryptSalt_value = loginhtml.xpath(
                "//input[@id='pwdDefaultEncryptSalt']/@value")  # P3jwGjbOCIeZIxJc

            password = encrypt_password(password, pwdDefaultEncryptSalt_value[0])
            auth_data = {
                "username": username,
                "password": password,
                "lt": lt_value[0],
                "dllt": dllt_value[0],
                "execution": execution_value[0],
                "_eventId": _eventId_value[0],
                "rmShown": rmShown_value[0],
            }

            auth_response = self.session.post(self.cas_url, data=auth_data, allow_redirects=False)
            if auth_response.status_code == 302:
                url_with_ticket = auth_response.headers["location"]
                confirm_response = self.svr_session.get(url=url_with_ticket, allow_redirects=False)


                # 打印响应头
                print(confirm_response.headers)
                location = confirm_response.headers["location"]
                ticket = re.findall(r"ticket=(.*)", url_with_ticket)
                JSESSIONID_auth = re.findall(r"JSESSIONID_auth=(.*);", str(confirm_response.headers))

                return ticket, JSESSIONID_auth, location


# 创建一个用于与应用服务器通信的 requests Session 对象
svr_session = requests.session()

# 创建一个 casService 对象
cas = casService(svr_session)

# 你的用户名和密码
your_username = "123456"
your_password = "12345678"

# 使用你的用户名和密码登录
ticket, jsessionid, location = cas.Login(serviceUrl="https://ehall.szu.edu.cn/appShow?appId=4997580036338493",
                               username=your_username, password=your_password)
```

### 第三个请求（认证页面的第二个请求）

#### 标头数据如下：

请求URL：[https://ehall.szu.edu.cn/login?service=https://ehall.szu.edu.cn/new/index.html&ticket=ST-61160-a7XFPjfZK1J1mZctUNDa1697096758336-ntS3-cas](https://ehall.szu.edu.cn/login?service=https://ehall.szu.edu.cn/new/index.html&ticket=ST-61160-a7XFPjfZK1J1mZctUNDa1697096758336-ntS3-cas)
请求方法：GET
请求标头：

```http
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding:gzip, deflate, br
Accept-Language:zh-CN,zh;q=0.9
Cache-Control:max-age=0
Connection:keep-alive
Cookie:route=4ef83fbd3cb79c7b0dd7289570453777; amp.locale=undefined; JSESSIONID=79YiyouLxOG199bVCD2tEhupwyy_NNjHjBhkNYD3UvEON7M7dJu6!840984734
Dnt:1
Host:ehall.szu.edu.cn
Referer:https://authserver.szu.edu.cn/
Sec-Ch-Ua:"Microsoft Edge";v="117", "Not;A=Brand";v="8", "Chromium";v="117"
Sec-Ch-Ua-Mobile:?0
Sec-Ch-Ua-Platform:"Windows"
Sec-Fetch-Dest:document
Sec-Fetch-Mode:navigate
Sec-Fetch-Site:same-site
Sec-Fetch-User:?1
Upgrade-Insecure-Requests:1
User-Agent:Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/117.0.0.0 Safari/537.36 Edg/117.0.2045.60
```

#### 负载部分如下：

查询字符串参数：

```http
service: https://ehall.szu.edu.cn/new/index.html
ticket: ST-61160-a7XFPjfZK1J1mZctUNDa1697096758336-ntS3-cas
```

#### 响应标头如下：

```http
Connection:keep-alive
Content-Length:142
Content-Type:text/html
Date:Thu, 12 Oct 2023 07:45:59 GMT
Location:https://ehall.szu.edu.cn:443/login?service=https://ehall.szu.edu.cn/new/index.html
Server:openresty
Set-Cookie:MOD_AUTH_CAS=MOD_AUTH_ST-61160-a7XFPjfZK1J1mZctUNDa1697096758336-ntS3-cas; path=/; Httponly
```

#### 分析：

可以看到发送了一个GET请求，请求正文是空的
可以发现其实是向上一个请求返回的重定向地址（已经带ticket了）发送了一个GET请求
这个请求是让CAS服务验证这个ticket是否有效
ticket验证通过后，会返回重定向地址指向最初请求的资源（`https://ehall.szu.edu.cn:443/login?service=https://ehall.szu.edu.cn/new/index.html`），并设置cookie(`Set-Cookie:MOD_AUTH_CAS=MOD_AUTH_ST-61160-a7XFPjfZK1J1mZctUNDa1697096758336-ntS3-cas; path=/; Httponly`)。

### 第四个请求（认证页面的第三个请求/返回重定向地址指向最初请求的资源）

#### 分析：

![7.png](https://s2.loli.net/2023/10/12/haHED8wKPULyGNj.png)
可以看到发送了一个GET请求，请求正文是空的
通过后，会返回重定向地址指向index页面的url地址。

### 第五个请求（认证页面的第四个请求/进入办事大厅）

#### 分析：

![8.png](https://s2.loli.net/2023/10/12/WYuZqnzTocybvBl.png)
可以看到发送了一个GET请求，请求正文是空的
通过后，状态代码改变了，不再是302重定向，而是304
此时服务器完成登录，会返回办事大厅的index界面

## 参考链接

[实现内网CAS统一认证登录](https://blog.csdn.net/zhutou_xu/article/details/114212377)

