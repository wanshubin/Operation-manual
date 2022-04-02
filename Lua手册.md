[TOC]



# 数据类型

可以使用 `type()` 函数来测试给定变量或者值的类型

<img src="C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331132214306.png" alt="image-20220331132214306" style="zoom:80%;" />

# 变量

Lua 声明变量时，不需要指定数据类型

变量名前面的 `local` 声明这是个局部变量，在命令行交互时，只有当前这一行可以访问 `local` 声明的局部变量，如果不加 `local`，那么声明为全局变量

![image-20220331132654518](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331132654518.png)



## 访问 table

```lua
-- 访问数组，lua数组的角标从1开始
print(arr[1])
-- 访问table
print(map['name'])
print(map.name)
```



## 字符串拼接

使用 `..` 来拼接字符串

```lua
local str1 = 'hello' .. 'world'
```



## 循环遍历

数组、table 都可以利用 for 循环来遍历

+   遍历数组

```lua
-- 声明数组 key为索引的 table
local arr = {'java', 'python', 'lua'}
-- 遍历数组
for index,value in ipairs(arr) do
	print(index, value)
end
```

+   遍历table

```lua
-- 声明map，也就是table
local map = {name='jack', age=21}
-- 遍历table
for key,value in pairs(map) do
	print(key, value)
end
```



# 函数

定义函数的语法：

```lua
function 函数名 (argument1, argument2..., argumentn)
	-- 函数体
	return 返回值
end
```

例如定义一个打印数组的函数：

```lua
function printArr(arr)
	for index, value in ipairs(arr) do
		print(value)
	end
end
```



# 条件控制

例如 if、else 语法：

```lua
if(布尔表达式)
then
	--[ 布尔表达式为 true 时执行该语句块 ]
else
	--[ 布尔表达式为 false 时执行该语句块 ]
end
```

![image-20220331135721287](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20220331135721287.png)