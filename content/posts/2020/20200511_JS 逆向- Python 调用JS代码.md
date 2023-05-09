---
title: JS逆向 - Python调用JS代码
date: 2020-05-11
lastmod: 2020-05-11
description: "JS逆向"

tags:
 - python
 - 爬虫
categories: 
 - 2020
 - 技术
---

:::tip
使用python执行JS代码
:::

<!-- more -->

## 简介
对于简单的 JS 来说，可以通过 Python 代码，直接重写，轻轻松松的就能搞定。 而对于复的 JS 代码，由于代码过于复杂，重写太费时费力，且碰到对方更新就比较麻烦。所以，一般直接使用程序去调用 JS，在 Python 层面就只是获取一个运行结果，这样做相比于重写而言就方便多了。

通常来说碰到 JS　逆向网站时会有这两种情况：
1. 简单 JS 破解：通过 Python 代码轻松实现。
2. 复杂的 JS 破解：代码不容易重写，使用程序直接调用 JS 运行获取结果。

## 1 Base64
Base64 是我们再写爬虫过程中经常看到的一种编码方式. 
``` py
// 原字符
NightTeam
// 编码之后的：
TmlnaHRUZWFt
```

``` py
// 原字符
aiyuechuang
// 编码之后的：
YWl5dWVjaHVhbmc=

// 原字符
Python3
// 编码之后
UHl0aG9uMw==
```

第二个例子是 aiyuechuang 编码之后的结果，它的末尾有一个等号，Python3 编码之后末尾有两个等号，这个特征相对第一个就比较明显。一般看到尾号有两个等号时应该大概可以猜到这个就是 Base64 。 然后，直接解码看一看，如果没有什么特别的话，就可以使用 Python 进行重写.

## 2 MD5
MD5 在 Javascript 中并没有标准的库，一般我们都是使用开源库去操作。
通常看到 32 位的一个英文数字混合的字符串，应该马上就能想到时 md5 了，这两个操作的话，因为在 Python 中都有对应的库，分别是：Base64 和 hashlib。
``` py
import base64  
str1 = b'aiyuechuang'
str2 = base64.b64encode(str1)
print(str2)
str3 = base64.b64decode('YWl5dWVjaHVhbmc=')
print(str3)
```

输出:
``` py
b'YWl5dWVjaHVhbmc='
b'aiyuechuang'
```

``` py
import hashlib

data = "aiyuechuang"
result = hashlib.md5(data.encode(encoding = "UTF-8")).hexdigest()
print(result)
```

输出：
``` py
e55babec7f5d5cf7bac7872f0481bec1
```

## 3 Python 调用 JS
Python 调用 JS 库的库。
* PyV8
* Js2Py
* PyExecJS
* PyminiRacer
* Selenium
* Pyppeteer

### 3.1 PyExecJS
* 诞生于 Ruby 中的库，后来被移植到了 Python 上
* 较新的文章一般都会说用它来执行 JS 代码
* 有多个引擎可选，但一般我们会选择使用 NodeJS 作为引擎来执行代码

### 3.2 PyExecJS 的使用
**安装 JS 运行环境Node.js**
> 注意：虽然 Windows 上有个系统自带的 JScript，可以用来作为 PyExecjs 的引擎，但是这个 JScript 很容易与其他的引擎有一个不一样的地方，容易踩到一些奇奇怪怪的坑。所以请大家务必要安装一个其他的引擎。比如说我们这里安装 Node.js 。

**安装 PyExecJS**
`pip install pyexecjs`

**检测运行环境**
IPython 终端执行
``` py
In [1]: import execjs

In [2]: execjs.get().name # 查看调用环境
Out[2]: 'Node.js (V8)'
```
execjs.get() # 查看调用的环境用此来看看我们的库能不能检测到 nodejs，如果不能的话那就需要手动设置。

``` py
# 长期使用
os.environ["EXECJS_RUNTIME"]="Node"

# 临时使用
import execjs.runtime_names
node=execjs.get(execjs.runtime_names.Node)
```
两种形式：一种是长期使用的，通过环境变量的形式，通过把环境变量改成大写的 EXECJS_RUNTIME 然后将其值赋值为 Node。 另一种的话，将它改成临时使用的一种方式，这种是直接使用 get，这种做法的话，在使用的时候就需要使用 node 变量了，不能直接导入 PyExecjs 来直接开始使用，相对麻烦一些

**使用**
``` py
from pprint import pprint

import execjs
import pathlib
import os

js_path = pathlib.Path(os.path.abspath(os.path.dirname(__file__)))
js_path = js_path / "crypto.js"
with js_path.open('r', encoding="utf-8") as f:
    script = f.read()

c = "1234"

# 传入python中的变量
add = ('''
aesEncrypt = function() {
    result={}
    var t = CryptoJS.MD5("login.xxx.com"),
        i = CryptoJS.enc.Utf8.parse(t),
        r = CryptoJS.enc.Utf8.parse("1234567812345678"),
        u = CryptoJS.AES.encrypt(''' + "'{}'".format(c) + ''',i, {
        iv: r
    });
        result.t=t.toString()
        result.i =i.toString()
        result.r =r.toString()
        result.u =u.toString()
        return result
    };
    ''')
script = script + add
print("script",script)

x = execjs.compile(script)
result = x.call("aesEncrypt")
print(result)
```

实际使用时，如果需要在 Python 中拿到 object 的话，建议把它转换成一个 json 字符串，而不是直接的把结果 return 出来。 因为，有些时候 PyExecjs 对 object 的转换会出现问题，所以可能会拿到一些类似于将字典直接用 str 函数包裹后转为字符串的一个东西，这样的话它是无法通过正常的方式去解析的

PyExecJS 存在的一些问题主要有以下两点：

* 执行大型 JS 时会有点慢（这个是因为，每次执行 JS 代码的时候，都是从命令行去调用到的 JS，所以 JS 代码越复杂的话，nodejs 的初始化时间就越长，这个基本上是无解的）

* 特殊编码的输入或输出参数会出现报错的情况（因为，是从命令行调用的，所以在碰到一些特殊字符输入或输出参数或者 JS 代码本身就有一些特殊字符的情况下，就会直接执行不了，给你抛出一个异常。不过这个跟系统的命令行默认编码有一定关系，具体的话这里就不深究了，直接就说解决方案吧。）

* 可以把输入或输出的参数使用 Base64 编码一下（如果看报错是 JS 代码部分导致的，那就去看看能不能删除代码中的那部分字符或者你自己 new 一个上下文对象，将那个名叫 tempfile 的参数打开，这样在调用的时候，它就直接去执行那个文件了，不过大量调用的情况下，可能会对磁盘造成一定压力。

* 而如果参数不充分导致的话，有个很简单的方法：就是把参数使用 Base64 编码一下，因为编码之后出来的字符串，我们知道 Base64 编码之后是生成英文和数字组成的。这样就没有特殊符号了。所以就不会出现问题了。）