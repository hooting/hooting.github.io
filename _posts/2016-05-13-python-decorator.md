
---
layout:     post
title:      "Python decorator 实例"
subtitle:   ""
date:       2016-05-13 12:00:00
author:     "Hooting"
header-img: "img/post-bg-2015.jpg"
tags:
    - python
    - decorator
---



python decorator使用的例子：获得方法执行的时间

```python
import time                                                

def timeit(method):

    def timed(*args, **kw):
        ts = time.time()
        result = method(*args, **kw)
        te = time.time()

        print '%r (%r, %r) %2.2f sec' % \
              (method.__name__, args, kw, te-ts)
        return result

    return timed

class Foo(object):

    @timeit
    def foo(self, a=2, b=3):
        time.sleep(0.2)

@timeit
def f1():
    time.sleep(1)
    print 'f1'

@timeit
def f2(a):
    time.sleep(2)
    print 'f2',a

@timeit
def f3(a, *args, **kw):
    time.sleep(0.3)
    print 'f3', args, kw

f1()
f2(42)
f3(42, 43, foo=2)
Foo().foo()

output:
f1
'f1' ((), {}) 1.00 sec
f2 42
'f2' ((42,), {}) 2.00 sec
f3 (43,) {'foo': 2}
'f3' ((42, 43), {'foo': 2}) 0.30 sec
'foo' ((<__main__.Foo object at 0x1066fa9d0>,), {}) 0.20 sec
```

从这里例子中，也可以知道参数`*args`和`**kw`的用法。即：

 - `*args`以`tuple`的形式包含所有的没有参数名的实参。
 - `**kw`以`dic`的形式包含所有的以`param=value`形式的参数
