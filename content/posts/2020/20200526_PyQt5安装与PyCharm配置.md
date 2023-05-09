---
title: PyQt5安装与PyCharm配置
date: 2020-05-26
tags:
 - 工具
 - 配置
categories:
 - 2020
 - 技术
---

:::tip
PyQt5安装与Qt Designer，PyUIC在 PyCharm 中配置
:::
<!-- more -->

## PyQt5,Qt Designer安装
``` py
pip install sip
pip install PyQt5
pip install PyQt5-tools
```

安装完成后，在python安装目录下可以看到下面标出的文件夹
![pyqt5_tools路径](https://gitee.com/chen-zq/bgimages/raw/master/img/pyqt5_tools%E8%B7%AF%E5%BE%84.png)

## 配置PyCharm
::: details 为什么配置PyCharm?
    配置PyCharm是为了在 Pycharm 里面实现打开 Qt Designer ，生成qt文件，方便转换成 python 文件.
:::

1. 打开Pycharm，settings 按下图操作
![pycharm配置pyqt5_tools](https://gitee.com/chen-zq/bgimages/raw/master/img/pycharm%E9%85%8D%E7%BD%AEpyqt5_tools.png)

Name：可自己定义

Program：指向上述安装PyQt5-tools里面的designer.exe

Work directory：使用变量 $FileDir$

2. 再新建一个“PyUIC”，这个主要是用来将 Qt 界面 转换成 py 代码
![pycharm配置PyUIC](https://gitee.com/chen-zq/bgimages/raw/master/img/pycharm%E9%85%8D%E7%BD%AEPyUIC.png)

Arguments的值改成下:

`-m PyQt5.uic.pyuic $FileName$ -o $FileNameWithoutExtension$.py`

## 使用
打开Qt Designer进行UI编辑
![打开Qt_Designer](https://gitee.com/chen-zq/bgimages/raw/master/img/%E6%89%93%E5%BC%80Qt_Designer.png)

设计UI
![20200526_设计UI层](https://gitee.com/chen-zq/bgimages/raw/master/img/20200526_%E8%AE%BE%E8%AE%A1UI%E5%B1%82.png)

保存后会在当前文件夹生成ui后缀的文件，点击ui文件右键 External Tool 》 PyUIC，会自动生成python文件，做一些编辑才能运行。两种方案：

1. 直接在当前文件添加以下代码
``` py
import sys
from PyQt5.QtWidgets import QApplication, QMainWindow

... ...

if __name__ == '__main__':
    app = QApplication(sys.argv)
    MainWindow = QMainWindow()
    ui = Ui_MainWindow()
    ui.setupUi(MainWindow)
    MainWindow.show()
    sys.exit(app.exec_())
```

2. 新建启动文件（方便逻辑层编写）
![创建启动文件](https://gitee.com/chen-zq/bgimages/raw/master/img/20200526_%E5%88%9B%E5%BB%BA%E5%90%AF%E5%8A%A8%E6%96%87%E4%BB%B6.png)
