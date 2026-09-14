---
layout: post
title: 2026-09-14 Bugku CTF 「矛盾」 - PHP 弱类型比较绕过 Writeup
date: 2026-09-14 15:00:00 +0800
categories: CTF Writeup
tags: [Bugku, PHP, 弱类型, Writeup]
---

# Bugku CTF - PHP 弱类型比较 (Web) Writeup

## 1. 题目信息

*   **平台**：Bugku CTF
*   **题目名称**：矛盾
*   **题目类型**：Web / PHP 代码审计
*   **考察知识点**：PHP 弱类型比较（Loose Comparison）、数组绕过、HTTP POST 请求。
*   **目标 URL**：`http://160.202.254.160:19246`

## 2. 题目源码分析

访问目标网址，页面回显了一段核心 PHP 代码：

```php
<?php
$what = $_POST['what'];
echo $what;
if($what == 'flag') {
    echo 'flag{****}';
}
?>

代码逻辑解读：

1. 程序通过 POST 方法接收一个名为 what 的参数，并赋值给变量 $what。
2. 程序会先输出 $what 的内容。
3. 接着使用 ==（弱等于）将 $what 与字符串 'flag' 进行比较。
4. 如果比较成立，则输出真正的 flag。

三、 漏洞原理

这是一个典型的 PHP 弱类型比较漏洞。

在 PHP 中，== 是弱等于比较符。当使用 == 比较一个字符串和一个数组时，PHP 会因为类型不匹配，直接导致比较条件成立（返回 true）。

即使不使用数组，如果使用的是纯数字字符串，也可能存在科学计数法绕过（例如 0e123 == 0e456 为 True）。但本题最直接的思路是使用数组绕过：
传入 what[]=1，此时 $what 是一个数组 Array([0] => 1)。由于数组不等于字符串 'flag'（类型不同），if($what == 'flag') 条件成立，从而执行 echo 'flag{****}';。

四、 解题过程

由于题目要求发送 POST 请求，无法直接在浏览器地址栏输入。可以使用浏览器开发者工具（F12）的控制台（Console）或者 HackBar 插件来发送 POST 请求。

步骤 1：打开开发者工具

在目标网页按 F12 打开开发者工具，切换到 控制台 (Console) 标签。

步骤 2：执行 JavaScript 请求代码

由于浏览器的安全机制，首次使用控制台粘贴代码时需要先输入 允许粘贴 并回车解除限制。

随后，在控制台输入以下 JavaScript 代码并执行（发送一个 POST 请求，参数为 what[]=1）：

```javascript
fetch('http://160.202.254.160:19246', {
    method: 'POST',
    headers: {'Content-Type': 'application/x-www-form-urlencoded'},
    body: 'what[]=1'
}).then(r => r.text()).then(t => alert(t));
```

步骤 3：获取结果

按下回车后，浏览器会弹出一个提示框（Alert），内容为服务器端的响应：

```
$what=$_POST['what'];<br>
echo $what;<br>
if($what=='flag')<br>
echo 'flag{****}';<br>

Array
```

为什么是 Array？
这里的 Array 就是你要提交的答案。
因为在 PHP 代码中，echo $what; 这行代码会把接收到的数组直接打印出来。PHP 中 echo 一个数组，只会输出单词 Array，而不会显示数组里面的具体值。
同时，if($what == 'flag') 条件成立，后台已经成功执行了输出 flag 的逻辑。

五、 最终 Flag

将弹窗中返回的 Array 作为答案提交到 CTF 平台即可。

(注意：部分平台可能要求提交完整格式，如果直接提交 Array 无效，请结合题目说明或靶场环境，检查 flag{****} 是否为隐藏占位符，但通常情况下此题 Array 即为最终答案)。

六、 总结与防御

· 漏洞成因：PHP 弱类型比较 == 在处理不同类型（如数组与字符串）时，没有进行严格的类型检查，导致判断逻辑被绕过。
· 防御方案：在开发中，对于这类需要严格校验的场景，应该使用 严格等于 ===（强类型比较），以确保比较双方的类型和值都完全相同，从而避免弱类型比较带来的安全风险。
