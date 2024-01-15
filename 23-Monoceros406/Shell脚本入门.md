---
title: Shell脚本入门
date: 2023-12-29 00:28:46
tags: Shell
mathjax: true
---

# Shell脚本入门

## 基础

```bash
declare -r TITLE="Page Title" #常量
a=z
b="a string"
c="a string and $b"
d="$(ls -l foo.txt)"
e=$((5*7))
f="\t\ta string\n"
mv "$filename" "${filename}1"

$? #上次命令的退出状态

echo $foo

cat<<__EOF__ #here document 引号不用转义
	$foo #some text
	"$foo" #"some text"
	'$foo' #'some text'
	\$foo #$foo
__EOF

function funct_2(){
	local foo; #局部变量
	foo=2;
	echo "foo=$foo"
}
echo $(funct_2)

clear
sleep 1
```

## if

```bash
x=5
if ["$x" -eq 5]; then
	echo "5" 
elif ["$x" -eq 6]; then
	echo "6"
else
	echo "no"
fi
```

常用文件表达式：

| 表达式  | 为真情况 |
| ------- | -------- |
| -e file | 存在     |
| -f file | 普通文件 |
| -d file | 目录     |
| -r file | 可读     |
| -w file | 可写     |
| -x file | 可执行   |

常用字符串表达式：

| 表达式                            | 为真情况 |
| --------------------------------- | -------- |
| string                            | 不为空   |
| -n string                         | 长度>0   |
| -z string                         | 长度=0   |
| string1==string2或string1=string2 |          |
| string1!=string2                  |          |
| string1<string2                   |          |
| string1>string2                   |          |

整数表达式：

| 表达式 | 为真情况 |
| ------ | -------- |
| -eq    |          |
| -ne    |          |
| -le    |          |
| -lt    |          |
| -ge    |          |
| -gt    |          |

组合表达式：

| 操作 | test | [[]]和(()) |
| ---- | ---- | ---------- |
| and  | -a   | &&         |
| or   | -o   | \|\|       |
| not  | !    | !          |

## test增强

```bash
if [["$INT" =- ^-?[0-9]+$ ]]; then #如果匹配正则表达式
	#...
fi

if ((INT==0)); then
	#...
fi   
```

## 控制操作符

```bash
[[-d temp]] || mkdir temp 
```

## 输入 

```bash
echo -n "input->" #-n不输出结尾换行符
read int1 int2 int3
echo $int1

#!/bin/bash
if read -t 10 -sp "input>" secret_pass; then
	echo -e "\npassphrase='$secret_pass'"
else
	echo -e "\ntimeout" >&2
	exit 1
fi
```

## IFS

```bash
#!/bin/bash
FILE=/etc/passwd
read -p "username>" user_name
file_info="${grep "^$user_name:" $FILE)"
if [-n "$file_info"]; then
	IFS=":" read user pw uid gid name home shell <<< "$file_info" #冒号分开
	echo "User='$user'"
	echo "UID='$uid'"
	echo "GID= '$gid'"
	echo "Full Name='$name'"
	echo "Home DIr='$home'"
	echo "Shell='$shell'"
else
	echo "no such user '$user_name'" >&2
	exit 1
fi 
```

## 验证输入

```bash
#文件名是否有效
if [["$REPLY"=-^[-[:alnum:]\._]_$]]; then
#是否浮点数
if [["$REPLY"=-^-?[[:digit:]]*\.[[:digit:]]+$]]; then
#是否整数
if [["$REPLY"=-^-?[[:digit:]]+$]]; then
```

## while/until

```bash
count=1
while [["$count" -le 5]]; do
	echo "$count"
	count=$((count+1))
done

#break continue略

count=1
until [["$count" -gt 5]]; do
	echo "$count"
	count=$((count+1))
done
```

## case

```bash
case "$REPLY" in
	0)	echo "xxx" #可正则
		exit
		;;& #passthrough语法
	1)	echo "xxx"
		uptime
		;;
	-h|--help)
		usage
		exit
		;;
	*)	echo "xxx" >&2
		exit 1
		;;
esac
```

## 命令行参数

```bash
echo "$#" #命令行传参个数
echo $0 $1 $2 ...

count=1
while [[$# -gt 0]]; do
	echo $1
	count=$((count+1))
	shift
done

#"word" "words with spaces"
echo $*
	#word
	#words
	#with
	#spaces
echo "$*":
	#word words with spaces
	#
	#
	#
echo $@:
	#同$*
echo "$@":
	#word
	#words with spaces
	#
	#
```

## for

```bash
for i in {A..D}; do
	echo $i;
done

for i in distros*.txt; do
	echo "$i";
done

for ((i=0;i<5;i=i+1)); do
	echo $i
done
```

## 字符串与数字

```bash
echo ${foo:-"substitute value if unset"}
echo ${foo:?"parameter is empty"}
echo ${foo:+"substitute value if set"}
echo ${!BASH*} #BASH开头的环境变量
echo ${#foo} #字符串长度
echo ${foo:5:6} #5开始的6个字符

foo=file.txt.zip
echo ${foo#*.} #删除模式*.匹配的后缀 最短
echo ${foo##*.} #同上 最长
echo ${foo%*.} #保留模式*.匹配的后缀 最短
echo ${foo%%*.} #同上 最长

foo=JPG.JPG
echo ${foo/JPG/jpg} #jpg.JPG
echo ${foo//JPG/jpg} #jpg.jpg
echo ${foo/#JPG/jpg} #jpg.JPG
echo ${foo/%JPG/jpg} #JPG.jpg

foo=aBc
echo ${foo,,} #abc 全部小写
echo ${foo,} #aBc 仅第一个小写
echo ${foo^^} #ABC 全部大写
echo ${foo^} #ABc 仅第一个大写

echo $((0xff))
echo $((2#11111111))
```

## 数组

```bash
a[1]=foo
echo ${a[1]}

declare -a a

days=(Sun Mon Tue Wed Thu Fri Sat)
days=([0]=Sun [1]=Mon [2]=Tue [3]=Wed [4]=Thu [5]=Fri [6]=Sat)

for i in $(animals[*]); do echo $i; done
for i in $(animals[@]); do echo $i; done
for i in "$(animals[*])"; do echo $i; done #拼接为一个字符串
for i in "$animals[@]"; do echo $i; done

echo ${#a[@]}

foo=([2]=a [4]=b [6]=c)
for i in "$(!foo[@])"; do echo $i; done #当索引不连续时查找索引
unset 'foo[2]'

foo=(a b c)
foo+=(d e f)

a=(f e d c b a)
a_sorted=($(for i in "$a[@]"; do echo $i; done | sort))
unset a

declare -A colors
colors["red"]="#ff0000"
echo ${colors["blue"]}
```

