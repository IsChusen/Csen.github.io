---
layout: post
title: "自动化抢飞天茅台"
date:   2023-3-24
tags: [script]
comments: false
author: chusen

---

## 自动化抢京东飞天茅台！

<!-- more -->

### 实现功能

> 脚本实现了自动抢京东茅台的功能



### 流程图



```flow
st=>start: 开始

op1=>operation: 设置url

op2=>operation: 设置headers

op3=>operation: 进入循环

op4=>operation: 发送请求

cond1=>condition: 请求成功？

op5=>operation: 判断是否开始抢购

cond2=>condition: 可以开始抢购？

op6=>operation: 发送加入购物车请求

cond3=>condition: 加入购物车成功？

op7=>operation: 发送进入结算页面请求

cond4=>condition: 进入结算页面成功？

op8=>operation: 结束

e=>end: 结束



st->op1->op2->op3->op4->cond1

cond1(yes)->op5

cond1(no)->op3

op5(yes)->cond2

op5(no)->op3

cond2(yes)->op6

cond2(no)->op3

op6(yes)->cond3

op6(no)->op3

cond3(yes)->op7

cond3(no)->op3

op7(yes)->cond4

op7(no)->op3

cond4(yes)->op8

cond4(no)->op3
```





### 实现方案

```python
import time
import requests
```

> 主要使用python的requests第三方库，建议python版本3.6以上





### 源码

```python
def auto_buy(cookie):
    url = "https://item.jd.com/100012043978.html"
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36 Edge/16.16299",
        "Referer": url,
        "Cookie": cookie
    }
    while True:
        try:
            r = requests.get(url, headers=headers)
            # 如果请求成功
            if r.status_code == 200:
                # 如果还未开始抢购
                if "抢购" in r.text:
                    print("还未开始抢购")
                # 如果可以开始抢购
                elif "立即抢购" in r.text:
                    print("开始抢购")
                    r = requests.get("https://cart.jd.com/gate.action?pcount=1&ptype=1&pid=100012043978",
                                     headers=headers)
                    # 如果加入购物车成功
                    if r.status_code == 200:
                        print("已加入购物车")
                        r = requests.get("https://trade.jd.com/shopping/order/getOrderInfo.action", headers=headers)
                        # 如果进入结算页面成功
                        if r.status_code == 200:
                            print("已进入结算页面")
                            break
                    # 如果加入购物车失败
                    else:
                        print("加入购物车失败")
                # 如果已经抢购过了
                else:
                    print("已经抢购过了")
                    break
            # 如果请求失败
            else:
                print("请求失败")
            time.sleep(0.1)
        except Exception as e:
            print(e)
            time.sleep(0.1)
```

```python
if __name__ == "__main__":
    # 需要去页面获取cookie传入
    cookie = ""
    auto_buy(cookie)
```





**如有不理解，欢迎邮件沟通**

