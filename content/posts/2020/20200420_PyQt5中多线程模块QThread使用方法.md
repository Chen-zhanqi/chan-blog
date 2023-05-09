---
title: PyQt5多线程模块QThread使用
date: 2020-04-20
tags:
 - 工具
categories:
 - 2020
 - 技术
---

:::tip
PyQt5中使用多线程QThread模块
:::
<!-- more -->

## 多线程模块QThread基本原理

**QThread是Qt的线程类中最核心的底层类。由于PyQt的的跨平台特性，QThread要隐藏所有与平台相关的代码 要使用的QThread开始一个线程，可以创建它的一个子类，然后覆盖其它QThread.run()函数**

``` py
class Thread(QThread):
    def __init__(self):
        super(Thread,self).__init__()
    def run(self):
```

接下来创建一个新的线程
``` py
thread = Thread()
thread.start()
```

可以看出，PyQt的线程使用非常简单，建立一个自定义的类（如Thread），自我继承自QThread ，并实现其run()方法即可。在使用线程时可以直接得到Thread实例，调用其start()函数即可启动线程，线程启动之后，会自动调用其实现的run()的函数，该方法就是线程的执行函数 。

业务的线程任务就写在run()函数中，当run()退出之后线程就基本结束了，QThread有started和finished信号，可以为这两个信号指定槽函数，在线程启动和结束之时执行一段代码进行资源的初始化和释放操作，更灵活的使用方法是，在自定义的QThread实例中自定义信号，并将信号连接到指定的槽函数，当满足一定的业务条件时发射此信号。

## QThread类中的常用方法

**start()**：启动线程

**wait()**：阻止线程，直到满足如下条件之一

> 1. 与此QThread对象关联的线程已完成执行（即从run返回时），如果线程完成执行，此函数返回True，如果线程尚未启动，也返回True

> 2. 等待时间的单位是毫秒，如果时间是ULONG_MAX（默认值·），则等待，永远不会超时(线程必须从run返回），如果等待超时，此函数将会返回False

**sleep()**：强制当前线程睡眠多少秒

### QThread类中的常用信号

  **started**：在开始执行run函数之前，从相关线程发射此信号

  **finished**：当程序完成业务逻辑时，从相关线程发射此信号

## 使用QThread重新实现程序解决问题

``` py
# -*- coding: utf-8 -*-

import sys
import time
from PyQt5.QtCore import QThread, pyqtSignal
from PyQt5.QtWidgets import QApplication, QMainWindow
from QThread_Example_UI import Ui_Form

class MyMainForm(QMainWindow, Ui_Form):
    def __init__(self, parent=None):
        super(MyMainForm, self).__init__(parent)
        self.setupUi(self)
        # 实例化线程对象
        self.work = WorkThread()
        self.runButton.clicked.connect(self.execute)

    def execute(self):
        # 启动线程
        self.work.start()
        # 线程自定义信号连接的槽函数
        self.work.trigger.connect(self.display)

    def display(self,str):
        # 由于自定义信号时自动传递一个字符串参数，所以在这个槽函数中要接受一个参数
        self.listWidget.addItem(str)

class WorkThread(QThread):
    # 自定义信号对象。参数str就代表这个信号可以传一个字符串
    trigger = pyqtSignal(str)

    def __int__(self):
        # 初始化函数
        super(WorkThread, self).__init__()

    def run(self):
        #重写线程执行的run函数
        #触发自定义信号
        for i in range(20):
            time.sleep(1)
            # 通过自定义信号把待显示的字符串传递给槽函数
            self.trigger.emit(str(i))

if __name__ == "__main__":
    app = QApplication(sys.argv)
    myWin = MyMainForm()
    myWin.show()
    sys.exit(app.exec_())
```

## 小结

如果实现的工具需要执行特别耗时的操作，可以使用本文多线程QThread处理方法实现。当然，工具实际实现过程中的场景会比这复杂。比如，你的输出并不是有固定时间间隔输出的文本框，可以尝试使用多次self.trigger.emit(str)方法进行操作。
