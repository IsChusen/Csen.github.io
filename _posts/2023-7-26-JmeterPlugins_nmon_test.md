---
layout: post
title: "JMeterPlugins+nmon实现服务器性能监测"
date:   2023-7-26
tags: [performance]
comments: false
author: chusen

---

<!-- more -->

### 实现方案

```python
JMeterPlugins、nmon实现服务器监控
```

> JMeterPlugins是jmeter插件，需要装ServerAgent服务[地址](https://blog.csdn.net/weixin_46579262/article/details/124167504)
>
> nmon为github上的开源项目（需服务器部署）[地址](https://gitee.com/goodhal/ezNmon-Manager)



### 实现功能

> 使用jmeter性能测试高并发，监控策略包括jmeter插件、服务器检测服务nmon



### 使用场景

> 项目性能测试报告



### 工具部署

jmeter安装插件后可在监听器上看到对应的监控

![image-20230619200209437](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-26-jmeter%2Bnmon/jmeter1.png)

![image-20230726172105799](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-26-jmeter%2Bnmon/jmeter2.png)



### nmon监控

nmon可实现常用的服务器监控

![image-20230726172126466](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-26-jmeter%2Bnmon/nmon1.png)

![image-20230726172131482](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-26-jmeter%2Bnmon/nmon2.png)



### 总结

通过jmeter+jp@gc - Permon Metrics Collector+nmon可实现常用性能压测的监控及报告 [相关包下载地址](https://pan.baidu.com/s/1Z53Gw3pJESWq1_ZCIbUO_g)(提取码：yfnu)

后续可以增加[jmeter5.1优化版](http://mzky.cc/post/75.html)新功能，生成表格性能测试报告![img](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-7-26-jmeter%2Bnmon/jmeter3.webp)

