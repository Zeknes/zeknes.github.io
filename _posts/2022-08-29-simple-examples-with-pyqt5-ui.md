---
layout: post
title: "一些PyQt5的示例代码"
subtitle: "Simple examples with PyQt5 and ui design"
author: "Eric"
header-style: text
tags:
  - PyQt5
  - PySide6
  - UI
  - Qt
---



## 目的



记录一下自己运用到的PyQt5代码, 以后再遇到类似的开发需要的时候, 直接修改复用就好, 而且, 这其中很多代码是我觉得比较典型的, 偶尔回顾浏览也比较有教学意义.



## 例子



### 1. Python 加载ui文件并实现一个按钮的示例代码



```python
from PyQt5.QtWidgets import QApplication, QPushButton
from PyQt5 import uic


class Stats:
    def __init__(self):
        # 从文件中加载UI定义
        # 从 UI 定义中动态 创建一个相应的窗口对象
        # 注意：里面的控件对象也成为窗口对象的属性了
        # 比如 self.ui.button , self.ui.textEdit
        self.ui = uic.loadUi("./ui/mainwindow.ui")
        # 信号和槽
        self.ui.pushButton.clicked.connect(self.handleCalc)

    def handleCalc(self):
        print("点击事件")


app = QApplication([])
stats = Stats()
stats.ui.show()
app.exec_()
```



PyQt5 使用QMessageBox的例子

```html
https://www.cnblogs.com/itwangqiang/articles/14955828.html
```





### 2. PyQt5 加载ui文件实现关机重启弹窗



```Python
from PyQt5.QtWidgets import QApplication, QPushButton, QMessageBox
from PyQt5 import uic
import os


class Stats:
    def __init__(self):
        # 从文件中加载UI定义
        # 从 UI 定义中动态 创建一个相应的窗口对象
        # 注意：里面的控件对象也成为窗口对象的属性了
        # 比如 self.ui.button , self.ui.textEdit
        self.ui = uic.loadUi("./ui/mainwindow.ui")
        # 信号和槽
        self.ui.pushButton.clicked.connect(self.handleBtnPoweroff)

    def handleBtnPoweroff(self):
        msgBox = QMessageBox()
        msgBox.setText("The document has been modified.")
        msgBox.setInformativeText("Do you want to save your changes?")

        reboot = msgBox.addButton("重启",QMessageBox.ApplyRole)
        poweroff = msgBox.addButton("关机",QMessageBox.ApplyRole)
        cancel = msgBox.addButton("取消",QMessageBox.DestructiveRole)

        reboot.clicked.connect(self.doReboot)
        poweroff.clicked.connect(self.doPowerOff)

        msgBox.setDefaultButton(cancel)

        ret = msgBox.exec_()


        print(ret)

    def doReboot(self):
        os.system("reboot")

    def doPowerOff(self):
        os.system("poweroff")


app = QApplication([])
stats = Stats()
stats.ui.show()
app.exec_()
```



### 3. 键盘监听关机按钮响应, 并实现关机功能. (可用于任何按键)



```python
from PyQt5.QtWidgets import QApplication, QPushButton, QMessageBox
from PyQt5 import uic
import os
from pynput import keyboard


class Stats:
    def __init__(self):
        # 从文件中加载UI定义
        # 从 UI 定义中动态 创建一个相应的窗口对象
        # 注意：里面的控件对象也成为窗口对象的属性了
        # 比如 self.ui.button , self.ui.textEdit
        self.ui = uic.loadUi("./ui/mainwindow.ui")
        # 信号和槽
        self.startKb()

    def on_press(self, key):
        try:
            print('\nalphanumeric key {0} pressed'.format(
                key.char))
        except AttributeError:
            print('special key {0} pressed'.format(
                key))
        pass

    def on_release(self, key):
        print('{0} released'.format(
            key))
        if key == keyboard.Key.esc:
            # Stop listener
            return False

        if str(key) == "<269025066>":
            print("you pushed power off button")
            self.handleBtnPoweroff()
            return True

    def startKb(self):
        listener = keyboard.Listener(
            on_press=self.on_press,
            on_release=self.on_release)
        listener.start()

    def handleBtnPoweroff(self):
        msgBox = QMessageBox()
        msgBox.setText("warning.")
        msgBox.setInformativeText("Are you sure to reboot?")

        reboot = msgBox.addButton("重启", QMessageBox.ApplyRole)
        poweroff = msgBox.addButton("关机", QMessageBox.ApplyRole)
        cancel = msgBox.addButton("取消", QMessageBox.ApplyRole)

        reboot.clicked.connect(self.doReboot)
        poweroff.clicked.connect(self.doPowerOff)

        msgBox.setDefaultButton(cancel)

        ret = msgBox.exec_()

        print(ret)

    def doReboot(self):
        os.system("reboot")

    def doPowerOff(self):
        os.system("poweroff")




app = QApplication([])
stats = Stats()
stats.ui.show()
app.exec_()

```

