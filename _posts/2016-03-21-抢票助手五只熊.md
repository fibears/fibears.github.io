---

layout: post
title: "讲座助手，五只熊"
subtitle: "\"Application!\""
date: 2016-03-21
author: "fibears"
header-img: "img/in-post/header/post-bg-robot.jpg"
tags:
- Python
- Tool

---

**Didn't reserve successfully for a lecture? No worry, my robot will help you secure your seat in advance, fast, free and friendly.**

**You can see the code in** [my github](https://github.com/fibears/LoginXMULecture).

## 引言

这个机器人出现的背景是酱的——作为Econ-XMU的研究生，我们每学期都必须听十次讲座。在讲座开始前，管理员会在固定时间点开放预约功能，我们需要登陆[这个网站](http://event.wisesoe.com/)进行预约，然而由于广大同学都太有学术热情，上半学期的讲座通常都会在一分钟之内被抢完。

作为一名懒人，我不喜欢每天固定时间点去蹲在电脑前，刷着网页等待预约系统的开放。又因为听讲座是个强制性的任务，加上前段时间刚刚接触`Python`的爬虫功能，所以我就尝试编写一个可以自动抢讲座的`Python`脚本。

由于爬虫的实质就是模拟登陆网页，并从网页中获取相关的信息。因此我们需要先了解下一些和网页相关的知识，比如数据载体HTML的基础知识、Python中相应的爬虫库以及正则表达式的使用方法。

> 具体内容可以参考我的上篇文章[http://fibears.top/2016/03/21/PythonCrawler/)。

## 准备工作

选择好的工具能够获得事半功倍的效果，我们需要的工具有：

1. Firefox 浏览器以及 Firebug、HttpFox插件
2. python2.7
3. urllib, urllib2, cookielib, lxml 软件库**(Ancaconda集成了这些软件库)**
4. UserName & Passwd

## 分析网页的运行逻辑

因为爬虫程序是模拟人登陆网页的过程，所以我们需要先分析网页的运行逻辑，此时我们可以借助强大的火狐浏览器。

![img1](/img/in-post/main/post-login-1.png)

如上图所示，点击 Net 选项，然后勾选 Persist 功能，以保证我们能完整地看到登陆的详细过程。接下来输入账号密码并点击登陆，密切注意着 Net 中的变化情况。

![img2](/img/in-post/main/post-login-2.png)

从 Net 的结果中我们可以看出，登录过程可以分成三个部分——登录、验证和跳转。其中登录过程发出两个 GET 请求，验证过程发出两个 GET 请求和一个 POST 请求，最后的跳转过程发出两个 GET 请求。接下来我们只要从这些请求中找出登录网站所需的 cookie 信息即可。

![img3](/img/in-post/main/post-login-3.png)

从上图中我们可以看出，登录该系统的最终网页(http://event.wisesoe.com/LectureOrder.aspx)需要 ASP.NET_Sessionld 和 .ASPXAUTH 这两个数据。经过分析我们发现可以从登陆过程发出的请求中获取到这两个数据，因此我们只需要一步步模拟发出 HTTP 请求并将 cookie 保存下来即可。


## 登录过程(getcookie.py)

1. 创建MozillaCookieJar对象实例来保存cookie，并写入文件；

2. 利用urllib2库的HTTPCookieProcessor对象来创建cookie处理器(opener)；

3. 合并用户数据、时间戳数据和 URL(http://account.wisesoe.com/WcfServices/SSOService.svc/Account/Logon?)，并利用处理器 opener 发出请求，获取cookie值；

4. 合并时间戳数据和 URL(http://account.wisesoe.com/WcfServices/SSOService.svc/Account/RequestToken?)，然后利用处理器 opener 发出请求，并从返回的结果中提取出 **token** 值；

5. 合并 **token** 值、头文件和 URL(http://event.wisesoe.com/Authenticate.aspx)，然后利用处理器 opener 发出请求，最后将 cookie 保存到本地文档中。

## 抢票过程(GrabLecture.py)

1. 载入 cookie 值，然后访问讲座系统网站(http://event.wisesoe.com/LectureOrder.aspx);

2. 对收到的响应文件进行解析，从 HTML 源码中提取出预约讲座时需要 POST 的数据；

3. 打包第二步的数据，并向原始网站发出预约指令。

4. DONE!


---

**祝大家抢票愉快！** ——Fibears

---

**新版本(2016-03-25): 支持输出已预约的讲座信息。**


> 备注：程序目前存在一个bug，现在只支持纯数字的密码。