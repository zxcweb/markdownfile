# Shell入门

## 介绍
一说到命令行，我们真正指的是shell。shell 就是一个程序，它接受从键盘输入的命令，然后把命令传递给操作系统去执行。几乎所有的 Linux 发行版都提供一个名为 bash的来自GNU项目的shell程序。“bash”是“Bourne Again SHell”的首字母缩写<br>
> Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。<br>
> Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

## 第一个shell脚本
```bash
#!/bin/sh
echo "hello shell!"
```
依国际惯例这里以在终端里打印一句hello shell!开始, 第一行的#!是一个约定标记, 它告诉脚本这段脚本需要什么解释器来执行. 第二行的echo命令则负责向屏幕上输出一句话.

## shell语法入门

### 注释

```bash
# 我是注释

:<<go
注释内容...
注释内容...
注释内容...
go

:<<!
注释内容...
注释内容...
注释内容...
!

test="test"
```

### 变量
和其它语言一样Shell中也有变量, 而且更简单，事实上shell变量只有一种字符串类型

#### 变量和使用

字符串是shell编程中最常用最有用的数据类型（除了数字和字符串，也没啥其它类型好用了），字符串可以用单引号，也可以用双引号，也可以不用引号。
```bash
# 单引号定义字符串
str='hello shell'
# 双引号定义字符串
str1="hello shell1"
# 什么都不加的定义字符串
str2=shell
# 变量的使用
echo $str1
# 变量在字符串中使用
str3="hello \"${str2}\"!"
# 单引号不能使用变量
str4='hello \"${str2}\"!' # 会输出：hello \"${str2}\"!
# 变量只读
str5=$str4
readonly str5
# 删除变量
unset str5
# 字符串长度
str6="hello shell"
echo ${#str6} #输出 11
# 截取字符串
echo ${str6:1:4} # 输出 ello
```

  - 双引号的优点：
  1. 双引号里可以有变量
  2. 双引号里可以出现转义字符

#### 变量的类型

1. 局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
```bash
func1()
{
  local num=222 #局部变量
  echo $num
}
echo $num
```

2. 环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。<br>
脚本在执行的时候会启动一个子shell进程：<br>
命令行中启动的脚本会继承当前shell的环境变量<br>
系统自动执行的程序脚本（非命令行启动）就需要自定义环境变量<br>

```bash
export VAR_NAME=VALUE 
```
3. 全局变量 在shell中，默认的变量作用域是全局类型的。
```bash
str='hello shell'
echo $str
```

### 数组

在Shell中，用括号来表示数组，数组元素用"空格"符号分割开。定义数组的一般形式为：<br>
数组名=(值1 值2 ... 值n)
```bash
# 定义数组
arr=("value1" "value2" "value3" "value4")
echo $arr
# 展开所有内容
echo ${arr[@]}
# 读取某一项
echo ${arr[0]}
# 赋值某一项
arr[0]="value"
echo ${arr[@]}
# 赋值不存在项
arr[8]="value8"
echo ${arr[@]}
echo ${arr[8]}
# 按顺序排列
arr[7]="value7"
echo ${arr[@]}
```

### 条件语句

基本语法结构:<br>
```bash
if condition
then 
	 # do something
elif condition
then 
	# do something
else
	# do something
fi
```
例子：
```bash
a=1
b=3
if [ $a == $b ]
then
    echo '相等'
else
    echo '不相等'
fi
```
### for循环

基本结构:
```bash
for name in "v1" "v2" "v3" ...
do
	# ...
done
```
例子：
```bash
# for in
for name in "v1" "v2" "v3"
do
	echo $name
done
# for
for (( i=0; i<5; i++ )); do
    echo $i
done
```
跳出循环break和continue

### 函数

要定义一个函数, 可以使用下面两种形式:
1. 可以带function fun() 定义，也可以直接fun() 定义,不带任何参数。
2. 参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。

```bash
# 无return
demoFun(){
    echo "这是我的第一个 shell 函数!$1 $2 $3"
}
echo "-----函数开始执行-----"
demoFun 1 2 3
echo "-----函数执行完毕-----"

# 有return
addFun(){
    return $(($1+$2))
}
addFun 1 2 3
echo $?
```

### 运算符

1. 算数运算符<br>
  - 乘号 * 前边必须加反斜杠 \ 才能实现乘法运算；<br>
 - 在MAC中shell的expr语法是：$((表达式))，此处表达式中的 * 不需要转义符号 \。
```bash
a=10
b=20
# 加法
val=`expr $a + $b`
echo "a + b : $val"
# 减法
val=`expr $a - $b`
echo "a - b : $val"
# 乘法
val=`expr $a \* $b`
echo "a * b : $val"
# 除法
val=`expr $b / $a`
echo "b / a : $val"
# 取余
val=`expr $b % $a`
echo "b % a : $val"
```
2. 关系运算符

|**运算符**|**说明**|**举例**|
|-------|-------|-------|
| -eq |	检测两个数是否相等，相等返回 true。| [ $a -eq $b ] 返回 false。|
|-ne	|检测两个数是否不相等，不相等返回 true。|	[ $a -ne $b ] 返回 true。|
|-gt	|检测左边的数是否大于右边的，如果是，则返回 true。|	[ $a -gt $b ] 返回 false。|
|-lt	|检测左边的数是否小于右边的，如果是，则返回 true。|	[ $a -lt $b ] 返回 true。|
|-ge	|检测左边的数是否大于等于右边的，如果是，则返回 true。|	[ $a -ge $b ] 返回 false。|
|-le	|检测左边的数是否小于等于右边的，如果是，则返回 true。|	[ $a -le $b ] 返回 true。|

```bash
a=10
b=20

if [ $a -eq $b ]
then
   echo "$a -eq $b : a 等于 b"
else
   echo "$a -eq $b: a 不等于 b"
fi
```
3. 布尔运算符

|**运算符**|**说明**|**举例**|
|-----|-----|-----|
|!	|非运算，表达式为 true 则返回 false，否则返回 true。	|[ ! false ] 返回 true。|
|-o	|或运算，有一个表达式为 true 则返回 true。	|[ $a -lt 20 -o $b -gt 100 ] 返回 true。|
|-a	|与运算，两个表达式都为 true 才返回 true。|	[ $a -lt 20 -a $b -gt 100 ] 返回 false。|

### shell内置两个交互性命令

1. read 读取一行数据并将其赋给一个变量
2. select in 可以显示出带编号的菜单，用户输入不同的编号就可以选择不同的菜单，并执行不同的功能。
```bash
select k in $list
do
  # do something
done
```

### 读取外部文件

source 相对路径




