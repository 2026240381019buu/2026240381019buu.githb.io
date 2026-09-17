---
title: 【Bugku CTF Writeup】Web-备份是个好习惯 (Backup is a Good Habit)
date: 2026-09-16 18:00:00 +0800
tags: [CTF, Bugku, Writeup, Web, 代码审计, MD5弱比较]
categories: 网络安全
---

## 一、题目信息

- **题目名称**：备份是个好习惯
- **题目类型**：Web
- **靶场来源**：Bugku CTF
- **考察知识点**：源码备份文件泄露、PHP 代码审计、str_replace 双写绕过、MD5 弱类型比较（0e 绕过/数组绕过）

## 二、解题过程

### 1. 信息收集与源码泄露
访问题目靶场链接，页面提示“备份是个好习惯”，这通常是暗示服务器上存在源码备份文件。尝试访问常见的备份文件名（如 `index.php.bak`、`index.php~` 等），成功获取到源码文件。

### 2. 代码审计
下载并查看 `index.php.bak` 的源码，核心逻辑如下：
```php
<?php
include_once "flag.php";
ini_set("display_errors", 0);
$str = strstr($_SERVER['REQUEST_URI'], '?');
$str = substr($str, 1);
$str = str_replace('key', '', $str);
parse_str($str);
echo md5($key1);
echo md5($key2);
if(md5($key1) == md5($key2) && $key1 !== $key2){
    echo $flag;
}
?>
