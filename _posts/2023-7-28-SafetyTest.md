---
layout: post
title: "ZAP实现安全扫描"
date:   2023-7-28
tags: [safety]
comments: false
author: chusen
---

<!-- more -->

### 实现方案

```python
使用ZAP工具进行安全扫描操作
```

### 界面简介

![image-20230728142640012](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/Safe1.png)

> 1.菜单栏，可进行文件、会话等的导入导出，视图设置，扫描策略分析，报告生成等等。
> 2.工具栏，可快速保存当前会话，设置界面布局，打开代理浏览器，生成报告等。
> 3.上下文及站点分布，双击“Default Content”可进行Session属性的配置，站点显示捕获到的URL，点击具体的请求则右边对应显示请求和响应的数据。
> 4.包含快速启动，请求和响应展示区。快速启动可选择自动扫描或手动探测。
> 5.历史访问记录，可按指定条件筛选展示，Alerts下记录扫描探测发现的漏洞问题。当执行扫描时，会显示扫描进度。



### 安全扫描

#### 自动扫描

在快速启动界面，点击“Automated Scan”进入自动扫描配置界面，只需要配置待攻击的网址，然后点击“Attack”执行傻瓜式的渗透测试。自动扫描无需配置代理。

![image-20230728142921489](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/Safe2.png)

自动扫描结束后可点击“Alerts”查看漏洞信息。

#### 手动扫描

手动扫描需先配置代理信息，需分别在ZAP的设置界面和浏览器设置相同的代理。

##### 设置代理

在工具栏选择“Options”按钮进入设置界面，选择“Local Proxies”，默认使用8080端口，也可自行设置端口号。然后在对应浏览器设置一样的代理信息就可以了。
有关浏览器的代理设置步骤可参考[此文章](https://www.python100.com/html/J61JC10BB12Z.html)

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/3.png)

> 注意：浏览器设置跟ZAP一样的代理之后，直接打开浏览器访问的记录是不会被ZAP截获的，需从ZAP界面工具栏打开浏览器进程访问待扫描网址。

##### 执行手动探测

在快速启动界面，点击“Manual Explore”进入手动扫描配置界面，输入网址后选择代理浏览器，点击“Launch Browser”打开代理浏览器访问待扫描系统。代理浏览器目前支持Firefox和Chrome。
如果访问成功，左侧Sites会记录截取到的请求及响应信息。尽量访问更多的页面以爬取出网站的所有链接页面。
也可以右键具体站点->Attack->选择“Forced Browse Site”、“Forced Browse Directory”、“Forced Browse Directory(and  Children)”->选择默认的 directory-list-1.0.txt 目录字典进行尝试爬取。

> 注：使用Forced xxx直接爬取的话范围比较广，个人倾向有针对性的手动探测。

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/4.png)

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/5.png)



##### 执行主动扫描

探测完成后，右键具体站点->Attack->选择“Active Scan”->使用默认策略->点击“Start Scan”开始扫描。

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/6.png)

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/7.png)

可查看扫描进度掌握进度情况：

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/8.png)

##### 查看扫描结果

点击下方历史记录栏的“Alerts”展开查看扫描出来的可能漏洞，点击具体漏洞则显示该漏洞的详细信息。

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/9.png)

#### 拦截请求

在工具栏，点击断点图标变成红色的状态，表示当前正在开启断点模式。在代理浏览器访问的请求会先到break里。修改参数后点击右向箭头会放行并向服务器提交请求参数。

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/10.png)

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/11.png)

放行后执行请求，断点状态恢复未开启状态，若需要再次设置断点则需重新点击断点图标为红色。

#### 重放攻击

> 重放攻击指攻击者重新发送一个目的主机已接收过的包，来达到欺骗系统的目的，主要用于身份认证过程，破坏认证的正确性。重放攻击可以由发起者，也可以由拦截并重发该数据的敌方进行。攻击者利用网络监听或者其他方式盗取认证凭据，之后再把它重新发给认证服务器。

在站点或者历史访问记录选中任一请求，右键选择“Open/Resend with Request  Editor”进入重放请求编辑界面，修改请求头/参数之后点击“Send”完成重放请求。我们一般从历史访问记录里找到某个请求，修改参数后再次send，用以验证是否存在潜在的安全漏洞问题。

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/12.png)

#### 暴力破解

> 暴力破解是使用攻击者自己的用户名和密码字典，一个一个去枚举，尝试是否能够登录。因为理论上来说只要字典足够庞大，枚举总是能够成功的！

我们通过代理抓到登录请求之后，在Request区域右键选择“Fuzz”进入爆破设置界面，选中需要参数化的部分，点击“Add”进入设置爆破内容，ZAP提供了多种Type的选择，枚举输入爆破内容后依次添加，点击“Start Fuzzer”开始执行爆破。

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/13.png)

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/14.png)

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/15.png)

可在历史栏->Fuzzer查看爆破结果。通过响应数据包Size Resp.Body排序，如果发现数据包明显大的则查看是否爆破成功。

![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/16.png)





### 效果图

![image-20230727172623746](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/17.png)

![image-20230727175628424](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-28-SafetyTest/18.png)





### 总结

本文主要讲了ZAP的代理、扫描、拦截、重放、爆破几个部分的操作步骤

查看[示例html报告](https://cloud.189.cn/t/fiMreifIrAni)（访问码：6vb7）

OWASP ZAP[下载地址](https://cloud.189.cn/t/zueaya7BzAZb) （访问码：1ihz）



**扫描所涉及的项目非公司项目**
