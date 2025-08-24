·

## Shell 编程

### 变量

```shell	
#!/bin/bash

# 赋值语句 等号左右不能有空格
a=1
b="$a" # "" 包围的语句会解析变量 -> 1
b1='$a' # -> $a
c=`ls` # `` 包围的语句在赋值时候就执行了
c1='la' # ``包围的语句是原字符串 
d=`touch ${c}` # ${var} 使用变量 \$用于转义
```

### 传参

```shell
#!/bin/bash
# 利用$0 $1 $.... 来表示命令行后的第0,1,2 ...个参数
# 第0个参数一定是脚本文件名
# $@输出参数字符串 $#输出参数个数
echo "the first argument is $1 $2 the arguments string is: $@"

```

### 数组

```shell
#!/bin/bash
# 数组可以不连续 类型不同
# 可以不初始化
array=(apple banana "fruit" abcde)
# ${#array[@]} 数组个数
array["${#array[@]}"]="aaaa"
echo ${array[${#array[@]}-1]}
# 在末尾添加数据
```

### 基本运算

```shell
#!/bin/bash
# $((expression))
# + - * / % **幂
A=1
B=$(($A*2+1)) # B=3
```

## 常用命令

### wc

### ls

### head

### tail

### awk

### grep

### cat

### sort