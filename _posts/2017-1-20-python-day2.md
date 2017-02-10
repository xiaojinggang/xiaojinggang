---
layout: post
title: Python基础-day2
categories: [blog ]
tags: [Film, Talk, ]
description: 测试环境的添加
---	   

# #Python数据类型

## #int（整数）

<pre>
在32位系统上，整数的位数为32位，取值范围为-2**31 ~ 2**31-1，即-2147483648～2147483647
在64位系统上，整数的位数为64位，取值范围为-2**63 ~ 2**63-1，即-9223372036854775808～9223372036854775807
</pre>

## #long（长整形）
python3不存在长整型，变为整型，需要注意：
<pre>
1、与C语言不同，Python的长整数没有指定位宽，
2、即：Python没有限制长整数型数值的大小，但实际上由于机器内存有限，我们使用的长整数数值不可能无限大。
3、注意，自从Python2.2起，如果整数发生溢出，Python会自动将整数转换为长整数，所以如今在长整数数据后面不加字母L也会导致严重后果！
</pre>

## #float（浮点型）
<pre>
1、浮点数用来处理实数，既带有小数的数字。
2、浮点表示的形式是小数；但小数不一定都是浮点型，23和52.3E-4是浮点数的例子。E标记表示10的幂。在这里，52.3E-4表示52.3*10的-4次幂
</pre>

## #complex（复数）
<pre>
复数由实数部分和叙述部分组成，一般形式为x+yj，其中的x是复数的实数部分，y是复数的虚数部分，这里的x和y都是实数。
注：Python中存在小数字池：
</pre>

## #布尔值

1或者0 

## #查看数据类型

<pre>
type("xiaojinggang")
str
</pre>




# #变量
<pre>
name = "XiaoJingGang"
name2 = name
print(name,name2)
print("# # # # # # # 更换变量后# # # # # # #")
name = "TongZhi"
print(name,name2)

执行结果：
XiaoJingGang XiaoJingGang
# # # # # # # 更换变量后# # # # # # #
TongZhi XiaoJingGang
</pre>

图解：
![](http://i1.piimg.com/567571/e502284cc8dd262b.png)

如果变量发生改变
![](http://p1.bpimg.com/567571/af66f7bdb0fce2a6.png)


# #代码优化

<pre>
如下一个if判断代码：
a=2
b=4
if a>b:
    c=a+b
else:
    c=a-b

将上述代码简化：
a=2
b=4
c=a+b if a>b else a-b

</pre>

# #数据运算

## 文件操作

<pre>
f = open('lyrics') #打开文件
first_line = f.readline()
print('first line:',first_line) #读一行
print('我是分隔线'.center(50,'-'))
data = f.read()# 读取剩下的所有内容,文件大时不要用
print(data) #打印文件
 
f.close() #关闭文件
</pre>

##　打开文件的模式有：

* r，只读模式（默认）。
 
* w，只写模式。【不可读；不存在则创建；存在则删除内容；】

* a，追加模式。【可读；   不存在则创建；存在则只追加内容；】

＃　＃字典

字典一种key - value 的数据类型，使用就像我们上学用的字典，通过笔划、字母来查对应页的详细内容。

语法格式：

<pre>
info = {
    'stu01': "XiaoJingGang",
    'stu02': "LiRuoTong",
    'stu03': "LiuYiFei",
}
</pre>

## 字典特性

* 无序性

* 去重，如：key必须是唯一的

### 增加

<pre>
info["stu04"] = "大力"
print(info)
{'stu04': '大力', 'stu01': 'XiaoJingGang', 'stu03': 'LiuYiFei', 'stu02': 'LiRuoTong'}
</pre>

### 修改

<pre>
info["stu01"] = "小精钢"
print(info)
{'stu04': '大力', 'stu03': 'LiuYiFei', 'stu01': '小精钢', 'stu02': 'LiRuoTong'}
</pre>

## 删除

<pre>
#方式一：pop
info.pop("stu01")
print(info)
{'stu03': 'LiuYiFei', 'stu02': 'LiRuoTong', 'stu04': '大力'}

#方式二：del
del info["stu02"]
print(info)
{'stu04': '大力', 'stu03': 'LiuYiFei', 'stu01': '小精钢'}

#方式三：popitem  #随机删除
info.popitem()
print(info)
{'stu02': 'LiRuoTong', 'stu04': '大力', 'stu03': 'LiuYiFei'}
</pre>

##查找
<pre>
>>> info = {'stu1102': 'LongZe Luola', 'stu1103': 'XiaoZe Maliya'}
>>> 
>>> "stu1102" in info #标准用法
True
>>> info.get("stu1102")  #获取
'LongZe Luola'
>>> info["stu1102"] #同上，但是看下面
'LongZe Luola'
>>> info["stu1105"]  #如果一个key不存在，就报错，get不会，不存在只返回None
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'stu1105'
</pre>

## 多级字典嵌套和操作

<pre>
item = {
    "主食":{
        "大米":{
            "米饭":{},
            "米粥":{},
        },
        "面粉":{
            "馒头":{},
            "烧饼":{},
        },
    },

    "饮品":{
        "碳酸":{
            "可乐":{},
            "雪碧":{},
        },
        "果汁":{
            "果粒橙":{},
            "汇源":{},
        },
    },
}

print(item["主食"]["面粉"])
#{'馒头': {}, '烧饼': {}}
</pre>

## 循环

<pre>
#方法1
for key in info:
    print(key,info[key])

#方法2
for k,v in info.items(): #会先把dict转成list,数据里大时莫用
    print(k,v)
</pre>

## 完整的三级菜单代码：


<pre>
##低端版本，通过多次循环进行选择。存在漏洞。代码有重复。重复次数与菜单的层次有关。
# Author:he li# Author:he li
# 三级菜单的编辑

item = {
    "主食":{
        "大米":{
            "米饭":{},
            "米粥":{},
        },
        "面粉":{
            "馒头":{},
            "烧饼":{},
        },
    },

    "饮品":{
        "碳酸":{
            "可乐":{},
            "雪碧":{},
        },
        "果汁":{
            "果粒橙":{},
            "汇源":{},
        },
    },
}


while True:
    for key in item:
        print(key)
    choose = input(">>:").strip()
    if len(choose) == 0 :continue
    if choose == "b":break
    if choose == "q":exit()
    if choose not in item:continue

    while True:
        for key2 in item[choose]:
            print(key2)
        choose2 = input(">>:").strip()
        if len(choose2) == 0 :continue
        if choose2 == "b":break
        if choose2 == "q":exit()
        if choose2 not in item[choose]:continue

        while True:
            for key3 in item[choose][choose2]:
                print(key3)
            choose3 = input(">>:").strip()
            if len(choose3) == 0 :continue
            if choose3 == "b":break
            if choose3 == "q":exit()
            if choose3 not in item[choose][choose2]:continue
</pre>

<pre>
##优化后的代码，不会受菜单的层次数的影响。
# Author:he li
# 三级菜单的编辑，根据选择，进入到下一级打印菜单并能进行选择。

item = {
    "主食":{
        "大米":{
            "米饭":{},
            "米粥":{},
        },
        "面粉":{
            "馒头":{},
            "烧饼":{},
        },
    },

    "饮品":{
        "碳酸":{
            "可乐":{},
            "雪碧":{},
        },
        "果汁":{
            "果粒橙":{},
            "汇源":{},
        },
    },
}

current_level = item
upper_level = []

while True:
    for key in current_level:
        print(key)
    choose = input(">>:").strip()
    if len(choose) == 0 :continue
    if choose == "q" : break
    if choose == "b":
        if len(upper_level) == 0 :break
        current_level = upper_level[-1]
        upper_level.pop()
    if choose not in current_level :continue
    upper_level.append(current_level)
    current_level = current_level[choose]

</pre>


##　针对列表的学历案例：
<pre>
##购物车：
# Author:he li
# 作业需求：
#    1、启动程序后，让用户输入工资，然后打印商品列表
#    2、允许用户更具商品编号购买商品
#    3、用户选择商品后，检测余额是否够用，够用就直接扣款，不够就提醒
#    4、可随时退出，退出时打印已购买商品和余额


salary=int(input("your salary in a month:"))

product=[
    ["iphone7",5500],
    ["pad",3000],
    ["bike",1500],
    ["cup",200],
    ["book",30],
]
shooping_car=[]
while True:
    for index,i in enumerate(product):
        print("%s: \t%s \t%s" %(index,i[0],i[1]))

    choose = input("输入你想要的商品的序号：")
    if choose.isdigit():
        choose=int(choose)
        if choose < len(product) and choose>=0:
            product_item=product[choose]   #购买的商品信息
            if salary > product_item[1]:  #商品买得起
                salary-=product_item[1]   #余额数
                shooping_car.append(product_item)   #追加到购物车
                print("\033[32;1m%s\033[0m 已加入到您的购物车，\
您的余额为：\033[32;1m%s\033[0m" %(product_item[0],salary))
            else:
                print("\033[31;1msorry\033[0m,您的余额不足，\
购买此商品还需要",product_item[1]-salary)

        else:
            print("\033[31;1msorry,没有您选择的商品！\033[0m")
    elif choose == "exit":
        total_cost = 0
        print("您当前购买的商品列表：")
        for j in shooping_car:
            print(j)
            total_cost +=j[1]

        print("您一共消费了:", total_cost)
        print("您当前余额为：",salary)
        break

    else:
        print("请输入正确的商品编号或输入exit退出")


</pre>