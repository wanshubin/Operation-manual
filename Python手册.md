[TOC]



# 数据类型

## 赋值

等号两侧应加上空格以保证格式规范

```python
x = 1
```



## 设置数据类型

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210903101927718.png" alt="image-20210903101927718" style="zoom:80%;" />

## 设定特定的数据类型/转换数据类型

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210903101829980.png" alt="image-20210903101829980" style="zoom:80%;" />



## 查看类型

```python
print(type(x))
```





# 输入

使用 input 进行输入，输入的类型为 string

```python
x = input("x: ")
```





# 注释

使用 `#` 来标记注释，Python中没有多行注释，因此可以在每行开头插入这个符号

因为Python将忽略未分配给变量的字符串文字，因此也可以使用`""" msg """` 来进行多行注释





# string 类型

## 使用单、双引号引用内容

```python
course = "Python Programming"
```



## 使用三对双引号规定输出文本格式

```python
course ="""
Python
Programming
"""
```



## len 函数返回 String 长度

```python
print(len(course))
```



## 使用下标返回对应位置的字符串

python中没有字符型的变量，所以一个字符的类型也是字符串

```python
print(course[0])
```

使用负数，从后向前的顺序索引，-1、-2、-3...

```python
print(course[-1])
```

返回对应段的字符串，如下从0开始，一直到2，并不包括3

```
print(course[0:3])
```

将返回原字符串

```
print(course[0:])
```

将返回从0开始到2的字符串

```
print(course[:3])
```



## 双引号的转义

使用 `\` 进行转义

```python
course = "Python \"Programming"
```



## 字符串连接

```python
first="Hello"
last="World"
full=first+" "+last
```

还可以使用字符串格式化，`{}`内的变量、表达式将会被解析，`{}`之间不能有表达式

```python
first="Hello"
last="World"
full=f"{first} {last}"
```



## 字符串处理

### 转换大小写

其中 title 将会让字符串每个单词开头大写，其余小写

```python
print(course.upper())
print(course.lower())
print(course.title())
```

### 去除空格

去除两端空格、去除左端空格和去除右端空格

```python
print(course.strip())
print(course.lstrip())
print(course.rstrip())
```

### 

## 查找字符串下标

查找不到时将返回 -1

```python
print(course.find(“pro”))
```



## 替换字符

```python
print(course.replace("p","j"))
```



## 判断字符串中是否（不）含有某个字符串

将返回 true 或 false

```python
print("pro" in course)
print("pro" not in course)
```





# 数值型

## Python的独特之处

### 取整除法

下面的语句将会返回 10 / 3 的取整结果（直接截断）

```python
print(10 // 3)
```

### 取余

下面的语句将会返回 10 对 3 的取余结果

```python
print(10 % 3)
```

## n次方

下面的语句将会返回 10 的 3 次方

```python
print(10 ** 3)
```



## 四舍五入

```python
print(round(2.9))
```



## 绝对值

```python
print(abs(-2.9))
```



## 引入 math 模块

通过如下语句引入 math 模块来使用许多数学函数

```python
import math
```





# bool 类型

+   0 
+   空字符串 ““ 
+   空list []

在转换为 bool 类型后都会为 false





# 控制语句

## if / elif / else 条件判定

python使用缩进来表示层次结构

```python
temperature = 15
if temperature > 30:
    print("it's warm")
    print("drink water")
elif temperature > 20:
    print("it's nice")
else:
    print("it's cold")
print("done")
```

```python
age = 22
message = "Eligible" if age >= 18 else "Not Eligible"
print(message)
```



## 逻辑运算符

and、or、not

它们也有着其他语言中逻辑运算符的“短路效应”



### python中可以使用连续的逻辑

以下两种条件的意义是一样的

```python
if age >= 18 and age < 65:
```

```python
if 18 <= age <65:
```



## 循环语句

### 使用 range() 配合 for 完成循环

如下语句 number 将经过0、1、2

```python
for number in range(3):
    print("Attempt", number)
```

如下语句 number 将经过1、2、3

```python
for number in range(1,4):
    print("Attempt", number)
```

如下语句 number 将经过1、3、5、7、9

```python
for number in range(1,10,2):
    print("Attempt", number)
```



### 使用 for 完成迭代器模式的遍历

```python
for x in "Python":
    print(x)
```



### break 跳出循环

如下的 else语句的内容 在 for 循环执行完毕后才会执行

```python
successful = True
for number in range(3):
    print("Attempt", number)
    if successful:
        print("successful")
        break
else:
    print("attempt 3 times failed")
```



### while 循环

```python
number = 200
while number > 0:
    print(number)
    number = number // 2
```

```python
command = ""
while command.lower() != "quit":
    command = input(">")
```

```python
while True:
    command = input(">")
    if command.lower() == "quit":
        break
```





# Function 方法、函数

## 定义一个带参方法

```python
def greet(first_name, last_name):
    print(f"Hi {first_name} {last_name}")
    print("Welcome aboard")


greet("Fury", "Wan")
```



## 定义一个有返回值的方法

如下代码是将函数返回值写入文件

```python
def get_greeting(name):
    return f"Hi {name}"


message = get_greeting("Wan")
file = open("content.txt", "w")
file.write(message)
```



## 设置默认参数

当没有传入给定参数时，参数将使用默认值，当传入了给定参数时，将会覆盖默认值

```python
def increment(number, by=1):
    return number + by


print(increment(2))
```



## 设置可变参数

#### 基于元组的参数列表

number 在此处将会是 [2, 3, 4, 5]

```python
def multiply(*numbers):
    result = 0
    for number in numbers:
        result = result + number
    return result


multiply(2, 3, 4, 5)
```



#### 基于字典的参数列表

user 为 {'id': 1, 'name': 'John', 'age': 22}

```python
def save_user(**user):
    print(user)


save_user(id=1, name="John", age=22)
```



## 变量范围

如下代码打印的 message 将会是 a，因为在方法内不会更改全局变量的值

```python
message = "a"


def greet(name):
    message = "b"


greet("Wan")
print(message)
```

### 使用 global 关键字

添加 global 关键字可以指定操作全局变量

```python
message = "a"


def greet(name):
    global message
    message = "b"


greet("Wan")
print(message)
```





# 数据结构

## list	列表

### 创建 list

```python
letters = ["a", "b", "c"]
martix = [[0,1],[2,3]]
zeros = [0] * 100

combined = zeros + letters

mumbers = list(range(20))

chars = list("Hello World")
```



### 读取 list

```python
letters = ["a", "b", "c", "d", "e"]
print(letters[0])
print(letters[-1])


letters[0] = "A"

# 读取0、1、2
print(letters[0:3])

# 每次读取的间隔步长为2，将会读取a、c、e
print(letters[::2])

# 从后向前读，每次读取的间隔步长为1，也就是会将 list 倒序输出
print(letters[::-1])
```



### list 的方法

#### append	追加

```python
letters = ["a", "b", "c"]
letters.append("d")
```

#### insert	插入

```python
letters = ["a", "b", "c"]
letters.insert(0, "/")
```

#### pop	弹出

pop没有参数时，默认弹出最后一个元素

```python
letters = ["a", "b", "c"]
letters.pop(0)
```

#### remove	移除

```python
letters = ["a", "b", "c"]
letters.remove("b")
```

#### del	删除

```python
letters = ["a", "b", "c"]
del letters[0:2]
```

#### clear	清空

```python
letters = ["a", "b", "c"]
letters.clear()
```

#### index	查看元素的下标

如果元素不存在则会直接报错，而不是返回 -1

```python
letters = ["a", "b", "c"]
print(letters.index("a"))
```

为了避免直接报错，可以采用如下方式

```python
letters = ["a", "b", "c"]
if "d" in letters:
    print(letters.index("d"))
```

#### count	元素个数

```python
letters = ["a", "b", "c"]
print(letters.count("d"))
```

#### sort	自身排序

默认按升序排序

```python
numbers = [3, 51, 2, 8, 6]
numbers.sort()
```

按降序排序

```python
numbers = [3, 51, 2, 8, 6]
numbers.sort(reverse=True)
```

#### sort	自定义排序

方法返回元组中的第二个元素也就是价格，在sort中定义key，并且引用方法（不是直接使用方法）

```python
items = [
    ("Product1", 10),
    ("Product2", 9),
    ("Product3", 12)
]


def sort_item(item):
    return item[1]


items.sort(key=sort_item)
print(items)
```

使用 lambda 函数

```python
items = [
    ("Product1", 10),
    ("Product2", 9),
    ("Product3", 12)
]

items.sort(key=lambda item: item[1])
print(items)
```

#### sorted	排序

sort 排序会改变原先列表的排序，而 sorted 却不会，它会返回一个新的 list

```python
numbers = [3, 51, 2, 8, 6]
numbers2=sorted(numbers,reverse=True)
```

#### map 对序列做映射

如下代码将会得到一个由价格组成的 list

它是通过 map 函数中使用 lambda 函数对 items 进行迭代然后转换为 list 得到的

```python
items = [
    ("Product1", 10),
    ("Product2", 9),
    ("Product3", 12)
]

prices = list(map(lambda item: item[1], items))
print(prices)
```

还可以使用更简单的 for 循环

```python
items = [
    ("Product1", 10),
    ("Product2", 9),
    ("Product3", 12)
]

prices = [item[1] for item in items]
print(prices)
```

#### filter 筛选

如下代码将得到由元组组成的 list，且价格大于等于10

```python
items = [
    ("Product1", 10),
    ("Product2", 9),
    ("Product3", 12)
]

filtered = list(filter(lambda item:item[1]>=10, items))
print(filtered)
```

还可以使用更简单的 for 循环

```python
items = [
    ("Product1", 10),
    ("Product2", 9),
    ("Product3", 12)
]

filtered = [item for item in items if item[1] >= 10]
print(filtered)
```

#### zip	压缩、元素交叉

```python
list1 = [1, 2, 3]
list2 = [10, 20, 30]

print(list(zip("abc", list1, list2)))

# 结果如下
[('a', 1, 10), ('b', 2, 20), ('c', 3, 30)]
```



### 解压列表

如下两种方式是相同的，值得注意的是，使用解压列表，变量个数要和 list 中元素的个数相同

```python
numbers = [1, 2, 3]
first, second, third = numbers
```

```python
numbers = [1, 2, 3]
first = numbers[0]
second = numbers[1]
third = numbers[2]
```

也可以把不想要的参数压缩在一个变量中

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8]
first, second, *other = numbers
```

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8]
first,  *other, last = numbers
```



### 遍历 list

使用 for 循环遍历

```
letters = ["a", "b", "c"]
for letter in letters:
    print(letter)
```

使用 enumerate 进行循环遍历，将会得到若干个下标和对应的元素组成的元组（类似于Map）

```python
letters = ["a", "b", "c"]
for letter in enumerate(letters):
    print(letter)
    
    
# 结果
(0, 'a')
(1, 'b')
(2, 'c')
```

```python
letters = ["a", "b", "c"]
for index, letter in enumerate(letters):
    print(index, letter)
    
    
#结果
0 a
1 b
2 c
```





## queue	队列

```python
from collections import deque
queue = deque([])
queue.append(1)
queue.append(2)
queue.append(3)
queue.popleft()
print(queue)

# 结果是
deque([2, 3])
```



## tuple	元组

元组是**只可读不可修改**的

```python
x = (1, 2, 3, 4, 5)
```

### 交换元素

其原理就是 tuple 的解压

```python
x = 5
y = 10
x, y = y, x
print("x: ", x)
print("y: ", y)
```



## array	数组

数组不像 list 一样，它只能存放类型相同的元素

它也有 append、remove、pop 等函数

```python
from array import array

numbers = array("i", [1, 2, 3])
print(numbers[0])
```



## set	集合

set中存放的元素是无序且唯一的

```python
numbers = [1, 1, 2, 3, 4]
uniques = set(numbers)
print(uniques)
```

使用 `|` 进行并集操作

```
numbers = [1, 1, 2, 3, 4]
first = set(numbers)
second = {1, 5}
print(first | second)
```

使用 `&` 进行交集操作

```python
numbers = [1, 1, 2, 3, 4]
first = set(numbers)
second = {1, 5}
print(first & second)
```

使用 `-` 进行差集操作

```python
numbers = [1, 1, 2, 3, 4]
first = set(numbers)
second = {1, 5}
print(first - second)
```

使用 `^` 进行补集操作

```python
numbers = [1, 1, 2, 3, 4]
first = set(numbers)
second = {1, 5}
print(first ^ second)
```



## dictionary	字典

这是一种类似于 map 的 key-value 键值对数据结构

其 key 值只能是 string 、元组 或者 数字，且不可改变，value 的值不受限制

```python
point = {"x": 1, "y": 2}
print(point["x"])
```

或者

```python
point = dict(x=1, y=2)
print(point["x"])
```

读取不存在的 key 将会直接报错，所以仍然可以使用 if-in 的形式

```python
if "a" in point:
    print(point["a"])
```

## dictionary 的操作

### get	读取

也可以使用 get 函数，它在读取不到时并不会报错，而是会返回 None

```python
point = dict(x=1, y=2)
print(point.get("a"))
```

还可以添加读取不到时的返回值，如下当读取不到将返回0

```python
point = dict(x=1, y=2)
print(point.get("a", 0))
```

### del	删除

```python
point = {"x": 1, "y": 2}
del point["x"]
```

### 遍历

```python
for x in point.items():
    print(x)
```

```python
for key in point.items():
    print(key, point[key])
```



## 解压

可以对 iterable 类型的变量进行解压操作，提取出它们其中的值

字典型只能有唯一key，所以如果在合并时有重复项，将会采用最后一个的值

```python
first = {"x": 1}
second = {"x": 10, "y": 2}
combined = {**first, **second, "z": 1}
print(combined)
```

