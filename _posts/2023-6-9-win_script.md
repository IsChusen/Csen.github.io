---
layout: post
title: "自动化办公脚本python"
date:   2023-6-9
tags: [script]
comments: false
author: chusen
---

## python实现自动化办公脚本

<!-- more -->

### 实现功能

> 脚本实现了通过截图与win页面对比，如果选择的图片在win页面上，则可以实现鼠标单击、双击、滚轮、键盘事件等。可执行单个、自定义组合、所有sheet页，实现DIY执行脚本更灵活



### 使用场景

> 自动化办公、游戏副本等



### 实现方案

```python
import pyautogui
import pyperclip
import xlrd
import time
```

> 主要使用python的pyautogui、pyperclip第三方库，建议python版本3.6以上



### 流程图

![win_ui](https://github.com/IsChusen/Csen.github.io/raw/master/images/june/win_ui.png)

### 源码

```python
def mouseClick(clickTimes, lOrR, img, reTry):
    if reTry == 1:
        while True:
            location = pyautogui.locateCenterOnScreen(img, confidence=0.9)
            if location is not None:
                pyautogui.click(location.x, location.y, clicks=clickTimes, interval=0.2, duration=0.2, button=lOrR)
                break
            print("未找到匹配图片,0.1秒后重试")
            time.sleep(0.1)
    elif reTry == -1:
        while True:
            location = pyautogui.locateCenterOnScreen(img, confidence=0.9)
            if location is not None:
                pyautogui.click(location.x, location.y, clicks=clickTimes, interval=0.2, duration=0.2, button=lOrR)
            time.sleep(0.1)
    elif reTry > 1:
        i = 1
        while i < reTry + 1:
            location = pyautogui.locateCenterOnScreen(img, confidence=0.9)
            if location is not None:
                pyautogui.click(location.x, location.y, clicks=clickTimes, interval=0.2, duration=0.2, button=lOrR)
                print("重复")
                i += 1
            time.sleep(0.1)


def dataCheck(sheet1):
    checkCmd = True
    # 行数检查
    if sheet1.nrows < 2:
        print("没数据")
        checkCmd = False
    # 每行数据检查
    i = 1
    while i < sheet1.nrows:
        # 第1列 操作类型检查
        cmdType = sheet1.row(i)[0]
        if cmdType.ctype != 2 or (cmdType.value != 1.0 and cmdType.value != 2.0 and cmdType.value != 3.0
                                  and cmdType.value != 4.0 and cmdType.value != 5.0 and cmdType.value != 6.0 and cmdType.value != 7.0):
            print('第', i + 1, "行,第1列数据不符合")
            checkCmd = False
        # 第2列 内容检查
        cmdValue = sheet1.row(i)[1]
        # 读图点击类型指令，内容必须为字符串类型
        if cmdType.value == 1.0 or cmdType.value == 2.0 or cmdType.value == 3.0:
            if cmdValue.ctype != 1:
                # print(cmdValue.ctype)
                print('第', i + 1, "行,第2列数据不符合")
                checkCmd = False
        # 输入类型，内容不能为空
        if cmdType.value == 4.0:
            if cmdValue.ctype == 0:
                print('第', i + 1, "行,第2列数据不符合")
                checkCmd = False
        # 等待类型，内容必须为数字
        if cmdType.value == 5.0:
            if cmdValue.ctype != 2:
                print('第', i + 1, "行,第2列数据不符合")
                checkCmd = False
        # 滚轮事件，内容必须为数字
        if cmdType.value == 6.0:
            if cmdValue.ctype != 2:
                print('第', i + 1, "行,第2列数据不符合")
                checkCmd = False
        # 键盘事件，内容必须为数字
        if cmdType.value == 7.0:
            if cmdValue.ctype != 1:
                print('第', i + 1, "行,第2列数据不符合")
                print("test")
                checkCmd = False
        i += 1
    return checkCmd


# 任务
def mainWork(sheet1):
    i = 1
    while i < sheet1.nrows:
        # 取本行指令的操作类型
        cmdType = sheet1.row(i)[0]
        if cmdType.value == 1.0:
            # 取图片名称
            img = sheet1.row(i)[1].value
            reTry = 1
            if sheet1.row(i)[2].ctype == 2 and sheet1.row(i)[2].value != 0:
                reTry = sheet1.row(i)[2].value
            mouseClick(1, "left", img, reTry)
            print("单击左键", img)
        # 2代表双击左键
        elif cmdType.value == 2.0:
            # 取图片名称
            img = sheet1.row(i)[1].value
            # 取重试次数
            reTry = 1
            if sheet1.row(i)[2].ctype == 2 and sheet1.row(i)[2].value != 0:
                reTry = sheet1.row(i)[2].value
            mouseClick(2, "left", img, reTry)
            print("双击左键", img)
        # 3代表右键
        elif cmdType.value == 3.0:
            # 取图片名称
            img = sheet1.row(i)[1].value
            # 取重试次数
            reTry = 1
            if sheet1.row(i)[2].ctype == 2 and sheet1.row(i)[2].value != 0:
                reTry = sheet1.row(i)[2].value
            mouseClick(1, "right", img, reTry)
            print("右键", img)
            # 4代表输入
        elif cmdType.value == 4.0:
            inputValue = sheet1.row(i)[1].value
            pyperclip.copy(inputValue)
            pyautogui.hotkey('ctrl', 'v')
            time.sleep(0.5)
            print("输入:", inputValue)
            # 5代表等待
        elif cmdType.value == 5.0:
            # 取图片名称
            waitTime = sheet1.row(i)[1].value
            time.sleep(waitTime)
            print("等待", waitTime, "秒")
        # 6代表滚轮
        elif cmdType.value == 6.0:
            # 取图片名称
            scroll = sheet1.row(i)[1].value
            pyautogui.scroll(int(scroll))
            print("滚轮滑动", int(scroll), "距离")
        # 7代表键盘Tab
        elif cmdType.value == 7.0:
            # 取图片名称
            keyboard = sheet1.row(i)[1].value
            if keyboard == 'tab':
                pyautogui.press('tab')
            elif keyboard == 'enter':
                pyautogui.press('enter')
            elif keyboard == 'backspace':
                pyautogui.press('backspace')
            elif keyboard == 'delete':
                pyautogui.press('delete')
            elif keyboard == 'up':
                pyautogui.press('up')
            elif keyboard == 'down':
                pyautogui.press('down')
            elif keyboard == 'left':
                pyautogui.press('left')
            elif keyboard == 'right':
                pyautogui.press('right')
            else:
                print('第', i + 1, "行,第2列数据不符合")
                checkCmd = False
            print("键盘操作:", keyboard)
        i += 1


def execute_script(sheet, keys):
    # 数据检查
    checkCmd = dataCheck(sheet)
    if checkCmd:
        key = keys
        if key == '1':
            # 循环拿出每一行指令
            mainWork(sheet)
        elif key == '2':
            while True:
                mainWork(sheet)
                time.sleep(0.1)
                print("等待0.1秒")
    else:
        print('输入有误或者已经退出!')


# 自定义组合执行sheet页的功能
def execute_custom_sheets(file, keys):
    # 打开文件
    wb = xlrd.open_workbook(filename=file)
    # 获取所有sheet页
    sheets = wb.sheets()
    # 打印sheet页名称
    for i in range(len(sheets)):
        print(i, sheets[i].name)
    sheet_indexes = input("请选择要执行的sheet页，输入数字(组合用','分隔)，或者输入all执行所有sheet页\n请输入：")

    if sheet_indexes == 'all':
        for sheet in sheets:
            print('--------开始执行', sheet.name, '--------')
            try:
                execute_script(sheet, keys)
            except Exception as e:
                print('执行出错:', e)
                input('按任意键继续...')
    else:
        sheet_index = sheet_indexes.split(',')
        for index in sheet_index:
            index = int(index)
            if index >= len(sheets):
                print('输入有误或者已经退出!')
            else:
                sheet = sheets[index]
                print('--------开始执行', sheet.name, '--------')
                try:
                    execute_script(sheet, keys)
                except Exception as e:
                    print('执行出错:', e)
                    input('按任意键继续...')
```

```python
if __name__ == '__main__':
    file = 'cmd.xls'
    keys = input('1.循环一次 2.死循环 \n请输入：')
    execute_custom_sheets(file, keys)
```





**如有不理解，欢迎邮件沟通**

