---
layout: post
title: "python性能测试方案"
date:   2023-6-19
tags: [performance]
comments: false
author: chusen

---

<!-- more -->

### 实现方案

```python
pip install locust
```

> python脚本主要使用python的locust，建议python版本3.7以上
>



### 实现功能

> locust使用协议层并发，可大致忽略压测机性能，把关注点更多放在结果上。



### 使用场景

> 项目性能测试



### 代码

```python
# 导入需要的模块
import os
from locust import HttpUser, task, between


# 定义用户行为
class WebsiteUser(HttpUser):
    # 设置等待时间
    wait_time = between(1, 3)

    # 平台登录接口压测
    @task
    def queryShardByAsset(self):
        ip = "192.168.44.11:80"
        url = f"http://{ip}/autotest/login"
        headers = {
            "Content-Type": "application/json;charset=UTF-8",
            "Cookie": "user=admin; pwd=123456; JSESSIONID=A8274D3A038539A1575100F8E7A23ADF0"
        }
        payload = {"account": "admin", "password": "TGl1bWFAMTIzNDU"}

        response = self.client.post(url, json=payload, headers=headers)
        print(response.json())


if __name__ == '__main__':
    # a = WebsiteUser.queryShardByAsset()
    os.system('locust -f .\test\locust.py --host=http://localhost:8001 --web-host=127.0.0.1')
```

<!--以上非公司代码-->

### LOCUST主页

![image-20230619184032230](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-6-19-performance/home.png)

实时结果（可视化）

![image-20230619184102512](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-6-19-performance/real_time.png)

### 导出报告

![image-20230619184114823](https://github.com/IsChusen/Csen.github.io/raw/master/images/2023-6-19-performance/export.png)



**自己编写的个人脚本，欢迎沟通。**

