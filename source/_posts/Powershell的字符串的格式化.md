---
title: PowerShell 或者说 .NET 中的字符串替换
date: 2024-09-27 19:32:18
tags:
---
主要参考了 MSDN 官网的[教程](https://learn.microsoft.com/zh-cn/powershell/scripting/learn/deep-dives/everything-about-string-substitutions)


## Concatenation 连接

首先即是各个编程语言均支持的语法糖：`+` 作为字符串拼接。

```powershell
$name = Xiaoming
$message = "Hello, " + $name
```

## Variable substitutions 变量替换

即直接使用 `$` 或 `$()` 在字符串里引用替换

```powershell
$first = "Li"
$last = "Xiaoming"
$message = "Hello, ${first} ${last}"
$msg = "Hello, $first $last"
```

## Command substitution 命令替换

```powershell
❯ $directory = Get-ChildItem -Path C:\Windows
❯ $message = "Time: $($directory.CreationTime)"  # 这里必须使用$()取值
❯ Write-Output $message
```

### Command execution 命令执行

```powershell
❯ "Date: $(Get-Date)"  # 此处时间的默认格式和 locale 的设置有关
Date: 09/28/2024 00:00:00
```

## Format string 格式化字符串

```powershell
# .NET 字符串格式化
❯ [string]::Format('Hello, {0} {1}', $first, $last)
# 或者是 powershell 中的
❯ 'Hello, {0} {1}' -f $first, $last
```

### Format values as arrays 将值格式化为数组

```powershell
# 即一种解包的方式
$values = @(
    "Xiaoming",
    "Lili"
)
'Hello, {0} {1}.' -f $values
```

## 高级格式设置

这是来自于 .NET 的方法，也就是 [`String.Format`](https://learn.microsoft.com/zh-cn/dotnet/api/system.string.format) 的方法
此处参考博客 [一个有故事的devops: powershell字符串操作](https://www.cnblogs.com/zqj-blog/p/10044181.html) 

对时间的字符串格式化

```powershell
❯ '{0:yyyy-MM-dd}' -f (Get-Date)
2024-09-28
```

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

常用的格式化的数值表格


|字符| 示例 | 说明 |
| :--:|:-- |:-- |
|`e` |   `>"{0:e2}" -f 2000` <br>`2.00e+003` |**e**xponent 科学计数法 |
| `d` | `>"{0:d}" -f 2000` <br>`2000` | **d**ecimal 整数 |
| `f` | `>"{0:f2}" -f 2000` |**f**ixed point 小数点后指定位数 | 
| `g` | `>"{0:g}" -f 2000` <br>`2000` | **g**eneral 小数点后最多一位，整数则不显示小数点 |
| `n` | `>"{0:n}" -f 2000` <br>`2,000` |**n**umber 默认小数点后两位，整数则不显示小数点 | 
| `c` | `>"{0:c}" -f 2000` <br>`¥ 2,000.00`| **c**urrency 货币格式，小数点后两位，整数则不显示小数点 | 
| `p` |  `>"{0:p}" -f 0.98765` <br>`98.76%` | **p**ercent 百分数，小数点后两位，整数则不显示小数点 |
| `x` |  `>"{0:x}" -f 2000` <br>`7d0` | he**x**adecimal 十六进制|

5. 字符串包含

```powershell
❯ "I have a dream".Contains("have")
True

❯ "I have a dream".Contains("i")
False

❯ "I have a dream" -match "i"
True

❯ "I have a dream" -cmatch "i"
False
```

`字符串.Contains`、`-cmath` 区分大小写，而 `-match` 忽略大小写

### 联接字符串

使用 `-join` 连接字符串

```powershell
❯ $server = @(
∙     'server1',
∙     'server2',
∙     'server3'
∙ )
❯ $server -join ','
server1,server2,server3
```

### `Join-Path`

`Join-Path` 是 PowerShell 中的一个内置函数，用于将多个路径元素组合成一个完整的路径。`

```powershell
❯ $folder = "Temp"
❯ Join-Path -Path 'C:\Windows' -ChildPath $folder
C:\Windows\Temp
```

## StringBuilder

`StringBuilder` 是 .NET Framework 中的一个类，用于在运行时动态地创建和修改字符串。它提供了一种更高效的方式来拼接字符串，因为它不会在每次拼接时都创建一个新的字符串对象，而是将拼接后的字符串追加到`StringBuilder`对象中。

```powershell
$stringBuilder = New-Object -TypeName "System.Text.StringBuilder"

[void]$stringBuilder.Append("Numbers: ")
foreach($number in 1..10000)
{
    [void]$stringBuilder.Append(" $number")
}
$message = $stringBuilder.ToString()
```

## 带大括号的描述符

`${<var names>}` 这样的调用方式能更好的明确边界

```powershell
Write-Host "$test $tester $($test)ter"
Write-Host "{0} {1} {0}ter" -f $test, $tester
```
