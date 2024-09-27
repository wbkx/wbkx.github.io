---
title: PowerShell 或者说 .NET 中的字符串替换
date: 2024-09-27 19:32:18
tags:
---
主要参考了 MSDN 官网的教程[^1^]

## Concatenation 连接

首先即是各个编程语言均支持的语法糖：`+` 作为字符串拼接。

```powershell
$name = Xiaoming
$message = "Hello, " + $name
```

## Variable substitutions 变量替换

```powershell
$first = "Li"
$last = "Xiaoming"
$message = "Hello, ${first} ${last}"
$msg = "Hello, $first $last"
```

<hr>
<p style="text-align: center">待整理</p>
<hr>

## Command substitution 命令替换

### Command execution 命令执行

## Format string 格式化字符串

### Format values as arrays

## 高级格式设置

这是来自于 .NET 的方法，也就是 `String.Format`[^2^] 的方法
此处参考博客 [powershell字符串操作](https://www.cnblogs.com/zqj-blog/p/10044181.html) [^3^]

1. 固定列宽

```powershell
$a = "name"
$b = "age"
$c = "sex"
# 分别指定 0，1，2，对应 $a, $b, $c，然后再指定宽度15左对齐
$d = "{0,-15}{1,-15}{2,-15}" -f $a, $b, $c  
Write-Output $d  # 输出 $d
```

2. 将数字转换成百分数

```powershell
"{0:p}" -f 3.14159  # 默认转换是两位

"{0:p5}" -f 3.1415926 # 也可以指定位数
```

3. 保留 n 位小数

```powershell
"{0:n0}" -f 3.14  # 3
"{0:n3}" - f 3.14159  # 3.141
```

4. 字符串替换


相关链接：

[^1^]: <https://learn.microsoft.com/zh-cn/powershell/scripting/learn/deep-dives/everything-about-string-substitutions>
[^2^]: <https://learn.microsoft.com/zh-cn/dotnet/api/system.string.format>
[^3^]: https://www.cnblogs.com/zqj-blog/p/10044181.html
