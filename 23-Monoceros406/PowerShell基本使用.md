---
title: PowerShell基本使用
date: 2024-01-06 09:42:09
tags: PowerShell
mathjax: true
---

# PowerShell基本使用

## 入门

```powershell
Get-Alias #输出内置别名
Get-Command -Name Get-Alias #显示Get-Alias命令类型
Get-Command -Verb Get #显示动词为Get的命令
Get-Command -Noun Content #名词为Content的命令
Get-Help Add-Content #获取Add-Content的帮助
Get-Help Add-Content -Examples #只获取示例部分
Get-Help about_Core_Commands #核心命令的“关于主题”
Get-Help -Name About* #About开头的帮助主题
Update-Help #更新帮助主题 开发者可能不提供
```

## 基本概念

```powershell
Set-StrictMode -Version Latest #启用严格模式 写脚本用 交互无所谓
$color='blue'
Set-Variable -Name color -Value blue
Get-Variable -Name color
$foo=$null
$LASTEXITCODE #最后一次操作退出码
Get-Variable -Name *Preference #查看所有偏好设置变量
$ErrorActionPreference='SilentlyContinue' #其他：High Continue Ignore
$foo=1
$foo='one'
$foo=$true

$num=1
$num.GetType().name #Int32
$num=1.5
$num.GetType().name #Double
[Int32]$num #2 四舍五入

"$color" #变量扩展
'$color' #变量不扩展

Select-Object -InputObject $color -Property * #查看color的所有属性
$color.Length
Get-Member -InputObject $color #查看color的所有方法
Get-Member -InputObject $color -Name Remove #查看Remove方法使用
$color.Remove(1,1)

$colorPicker=@('blue','white','yellow','black')
$colorPicker[0]
$colorPicker[1..3]
$colorPicker=$colorPicker+'orange'
$colorPicker+='brown'
$colorPicker+=@('pink','cyan')

$colorPicker=[System.Collections.ArrayList]@['blue','white','yellow','black']
$null=$colorPicker.Add('gray') #添加并防止输出
$colorPicker.Remove('gray') #删除离开头最近的元素

$users=@{
	abertram='Adam Betram';
	raquelcer='Raquel Cerillo';
	zheng21='Justin Zheng'
}
$users['abertram']
$users.abertram
$users.Keys #获取所有键
$users.Values #获取所有值
$users.Add('natice','Natalie Ice')
$users['phrigo']='Phil Rigo'
$users.ContainsKey('johnnyq')
$users.Remove('natice')

$myFirstCustomObject=New-Object -TypeName PSCustomObject
$myFirstCustomObject=[PSCustomObject]@{OSBuild='x';OSVersion='y'}
$myFirstCustomObject.OSBuild
```

## 组合命令

```powershell
$serviceName='wuauserv'
Get-Service -Name $serviceName
Start-Service -Name $serviceName

Get-Service -Name 'wuauserv' | Start-Service
'wuauserv' | Get-Service | Start-Service

Get-Content -Path C:\Services.txt | Get-Service | Start-Service #Get-Content从文件一行一行读数据，一个一个发送而不是发送数组

$serviceObject=[PSCustomObject]@{Name='wuauserv';ComputerName='SERV1'}
$serviceObject | Get-Service

Get-ExecutionPolicy
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned #Restricted AllSigned RemoteSigned Unrestricted

Write-Host 'aaa' #输出
```

## 控制流

```powershell
#远程计算机文件读取
Get-Content -Path "\\服务器名\c$\App_configuration.txt"

#表达式
1 -eq 1 #True
#其他 -ne -gt -ge -lt -le -contains -not

Test-Connection -ComputerName 服务器名 -Quiet -Count 1 #尝试ping通 -Quiet强制只返回布尔，否则一大堆信息 -Count限制只尝试一次
-not(Test-Connection -ComputerName 服务器名 -Quiet -Count 1) #翻转语法

if(-not (Test-Connection -ComputerName $servers[0] -Quiet -Count 1)){
	Write-Error -Message "The server $servers[0] is not responding!"
}elseif{
	Write-Error -Message "The server $server[0] does not have the right file!"
}else{
	Get-Content -Path "\\$servers[0]\c$\App_configuration.txt"
}

$currentServer=$servers[0]
switch($currentServer){
	$servers[0]{
		#...
		break
	}
	$servers[1]{
		#...
		break
	}
}

foreach($server in $servers){
	Get-Content -Path "\\$server\c$\App_configuration.txt"
}
ForEach-Object -InputObject $servers -Process{
	Get-Content -Path "\\$_\c$\App_configuration.txt"
}
$servers | ForEach-Object -Process{
	Get-Content -Path "\\$_\c$\App_configuration.txt"
}
$servers.foreach({Get-Content -Path "\\$_\c$\App_configuration.txt"})

$servers=@('SERVER1','SERVER2')
for($i=0;$i -lt $servers.Length;$i++){
	$servers[$i]="new $(servers[$i])"
}

while(Test-Connection -ComputerName $problemServer -Quiet -Count i){
	Get-Content -Path "\\$problemServer\c$\App_configuration.txt"
	break
}

do{
	$choice=Read-Host -Prompt 'What is the best programming language?'
}until($choice -eq 'PowerShell')
Write-Host -Object 'Correct!'
```

## 错误处理

```powershell
#错误转换成中执行错误
$ErrorActionPreference=Stop #默认Continue继续执行cmdlet Ignore不输出也不记录到$Error Inquire输出并询问 SilentlyContinue不输出但记录到$Error

$folderPath='.\bogusFolder'
try{
	$files=Get-Childitem -Path $folderPath -ErrorAction Stop
	$files.foreach({
		$fileText=Get-Content $files
		$fileText[0]
	})
}catch{
	$_.Exception.Message
}
```

## 函数

```powershell
function Install-Software{
	#...
}
Install-Software

function Install-Software{
	[CmdletBinding()]
	param(
		[Parameter()] #如果()填Mandatory 则参数强制 否则终止询问
		[ValidateSet('1','2')] #只能选'1'或'2' 否则报错
		[string]$Version=2 #如果不填默认2
		
		[Parameter(Mandatory,ValueFromPipeline)]
		[string]$ComputerName
	)
	process{ #如果不是从管道接收参数 process块可去掉
		Write-Host "Installed $Version on $ComputerName"
	}
}
Install-Software -Version 2
```

## 模块

```powershell
Get-Module #查看导入的模块
Get-Command -Module Microsoft.PowerShell.Management #查看指定模块中的命令
Get-Module -ListAvailable #查看所有可用模块
#模板后缀名.psm1 查找以下路径：
#C:\Windows\System32\WindowsPowerShell\1.0\Modules
#C:\Programe Files\WindowsPowerShell\Modules
#C:\Users\<LoggedInUser>\Documents\WindowsPowerShell\Modules

#当前会话临时导入模块路径
$env:PSModulePath+';C:\MyNewModulePath'
#持久改动
$CurrentValue=[Environment]::GetEnvironmentVariable("PSModulePath","Machine")
[Environment]::SetEnvironmentVariable("PSModulePath",$CurrentValue+";C:\MyNewModulePath","Machine")

Import-Module -Name Microsoft.PowerShell.Management #导入模块
Import-Module -Name Microsoft.PowerShell.Management -Force #重新导入
Remove-Module -Name Microsoft.PowerShell.Management #卸载
```

