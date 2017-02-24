---
layout: post
title: python-生成器
categories: [blog ]
tags: [Film, Talk, ]
description: Python路
---	   

# 生成器

  通过列表生成式，我们可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费。

  所以，若果列表元素可以按照某种算法推算出来，那我们是否可以再循环的过程中不断腿粗安出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。


    >>> L = [x * x for x in range(10)]
	>>> L
	[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
	>>> g = (x * x for x in range(10))
	>>> g
	<generator object <genexpr> at 0x1022ef630>



  创建L和g的区别仅在于最外层的[]和()，L是一个list，而g是一个generator。

  我们可以直接打印出list和每一个元素，但我们怎么打印出generator的每一个元素呢？

  如果要一个一个打印出来，可以通过next()函数获得generator的下一个返回值：


	>>> next(g)
	0
	>>> next(g)
	1
	>>> next(g)
	4
	>>> next(g)
	9
	>>> next(g)
	16
	>>> next(g)
	25
	>>> next(g)
	36
	>>> next(g)
	49
	>>> next(g)
	64
	>>> next(g)
	81
	>>> next(g)
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	StopIteration


  generator保存的是算法，每次调用next(g)，就计算出g的下一个元素的值，知道计算到最后一个元素，没有更多的元素时，抛出StopIteration的错误。

  当然，上面这种不断调用next(g)实在是太变态了，正确的方法是使用for循环，因为generator也是可迭代对象：


	>>> g = (x * x for x in range(10))
	>>> for n in g:
	...     print(n)
	...
	0
	1
	4
	9
	16
	25
	36
	49
	64
	81


  所以创建了一个generator后，基本上永远不会调用next()，而是通通过for循环来迭代它，并且不需要关心stopIteration的错误。

  generator费城强大。如果推算的算法比较复杂，用类似列表生成式的for循环无法实现的额时候，还可以用函数来实现。

  比如，著名的裴波拉契数列（Fibonacci），除第一个和第二个数外，任意一个数都可以由前两个数相加得到：

>1, 1, 2, 3, 5, 8, 13, 21, 34, ...

  裴波拉契数列用列表生成是写不出来，但是，用函数把它打印出来去很容易：


	def fib(max):
	    n, a, b = 0, 0, 1
	    while n < max:
	        print(b)
	        a, b = b, a + b
	        n = n + 1
	    return 'done'


  注意赋值语句：

	a,b = b, a + b

  相当于：


	t = (b, a + b) # t是一个tuple
	a = t[0]
	b = t[1]


但不必显示出临时变量t就可以赋值。

上面的函数可以输出裴波拉契数列的前N个数：


	>>> fib(10)
	1
	1
	2
	3
	5
	8
	13
	21
	34
	55
	done


  仔细观察，可以看出，fib函数实际上是定义了裴波拉契数列的推算规则，可以从第一个元素开始，推算出后续任意的元素，这种逻辑其实非常类似generator。

  也就是说，上面的函数和generator仅一步之遥。要把fib函数变成generator，只需要把print(b)改为yield b 就可以了：


	def fib(max):
	def fib(max):
	    n,a,b = 0,0,1
	
	    while n < max:
	        #print(b)
	        yield  b
	        a,b = b,a+b
	
	        n += 1
	
	    return 'done' 


  这就是定义generator的另一种方法。如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator：

<pre>
>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>
</pre>

  这里，最难理解的就是generator和函数的执行流程不一样。函数是顺序执行，遇到return语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句出继续执行。

<pre>
data = fib(10)
print(data)

print(data.__next__())
print(data.__next__())
print("干点别的事")
print(data.__next__())
print(data.__next__())
print(data.__next__())
print(data.__next__())
print(data.__next__())

#输出
<generator object fib at 0x101be02b0>
1
1
干点别的事
2
3
5
8
13
</pre>

  上面的例子，再循环过程中不断调用yield，就会不断中断。当然要给循环设置一个条件来退出循环，不然就会产生一个无限数列出来。

  同样的，吧函数改成generator后，我们基本上从来不会用next()来吼去下一个返回值，而是直接使用for循环来迭代：

<pre>
>>> for n in fib(6):
...     print(n)
...
1
1
2
3
5
8
</pre>

  但是用for循环调用generator时，发现拿不到generator的return语句的返回值。如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中：

<pre>
>>> g = fib(6)
>>> while True:
...     try:
...         x = next(g)
...         print('g:', x)
...     except StopIteration as e:
...         print('Generator return value:', e.value)
...         break
...
g: 1
g: 1
g: 2
g: 3
g: 5
g: 8
Generator return value: done
</pre>

  关于如何捕获错误，后面的错误处理还会详细讲解。

  还可通过yield实现再单线程的情况下实现并发运算的效果

<pre>
#_*_coding:utf-8_*_
__author__ = 'xiaojinggang'

import time
def consumer(name):
    print("%s 准备吃包子啦!" %name)
    while True:
       baozi = yield

       print("包子[%s]来了,被[%s]吃了!" %(baozi,name))


def producer(name):
    c = consumer('A')
    c2 = consumer('B')
    c.__next__()
    c2.__next__()
    print("老子开始准备做包子啦!")
    for i in range(10):
        time.sleep(1)
        print("做了2个包子!")
        c.send(i)
        c2.send(i)

producer("xiaojinggang")
</pre>

