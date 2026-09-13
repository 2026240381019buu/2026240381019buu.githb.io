---

title: 【Bugku CTF Writeup】Web 入门两则（GET 传参 & 头等舱 HTTP 响应头信息泄露）
date: 2026-09-13 16:00:00
tags:

· CTF
· Bugku
· Writeup
· Web安全
  categories:
· 网络安全

---

前言

本文汇总了 Bugku CTF Web 方向的两道入门题：GET 与 头等舱。两题难度都不高，但分别对应了两个非常基础且重要的知识点——HTTP GET 请求传参 和 HTTP 响应头信息泄露，适合刚接触 CTF Web 的新手练手。

---

一、GET（HTTP GET 请求传参）

1. 题目信息

· 题目名称：GET
· 题目类型：Web
· 靶场来源：Bugku CTF

2. 解题过程

（1）启动环境，查看页面源码
启动靶机后，打开题目页面，页面通常会直接显示一段 PHP 源码，核心逻辑大致如下：

```php
<?php
$what = $_GET['what'];
if ($what == 'flag') {
    echo "flag{...}";
}
?>
```

从源码可以看出，程序通过 $_GET['what'] 接收 URL 中传递的 what 参数，并判断其值是否等于字符串 flag。若条件成立，则输出真正的 Flag。

（2）分析题目名称，确定解题方向
题目名为「GET」，这是一个非常直接的提示：

· 「GET」对应 HTTP 协议中的 GET 请求方法；
· 源码中的 $_GET 也印证了考点就是 URL 传参；
· 因此只需要在 URL 中构造 ?what=flag 即可满足条件。

（3）构造 Payload 并访问
GET 请求的参数直接拼接在 URL 后面，格式为 ?参数名=参数值。根据源码要求，参数名为 what，值为 flag，构造如下 Payload：

```
http://<题目地址>/get/?what=flag
```

访问该链接，页面即可回显 Flag。也可以使用命令行工具验证：

```bash
curl "http://<题目地址>/get/?what=flag"
```

（4）提取 Flag
在页面回显中拿到 Flag 字符串。
最终提交的 Flag 是：flag{这里填入你实际提交的flag内容}

3. 总结与心得

这道题是典型的 HTTP GET 请求传参，是 CTF Web 方向最基础的入门题。它让我明白了：

1. 看到 PHP 源码中的 $_GET['xxx']，就要想到在 URL 中构造 ?xxx=值 进行传参。
2. GET 请求的参数以键值对形式附加在 URL 后，用 ? 起始，多个参数之间用 & 分隔。
3. 题目名称往往就是最直接的提示，比如「GET」直接指向了请求方法与传参方式。

---
