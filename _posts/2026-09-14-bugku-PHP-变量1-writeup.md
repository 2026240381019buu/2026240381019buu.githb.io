---
layout: post
title: "2026-09-14 Bugku CTF 「变量1」- 超全局变量与 eval 绕过 Writeup"
date: 2026-09-14 15:30:00 +0800
categories: CTF Writeup
tags: [Bugku, PHP, 变量覆盖, 超全局变量, Writeup]
---

# Bugku CTF - 「变量1」 Writeup

## 1. 题目信息
- **平台**: Bugku CTF
- **题目类型**: Web / PHP 代码审计
- **考点**: 正则表达式绕过、`eval()` 函数执行、PHP 超全局变量 `$GLOBALS`

## 2. 题目源码
访问题目环境，页面直接给出了 PHP 源码：

```php
flag In the variable ! <?php

error_reporting(0);
include "flag1.php";
highlight_file(__file__);
if(isset($_GET['args'])){
    $args = $_GET['args'];
    if(!preg_match('/^\w+$/', $args)){
        die("args error!");
    }
    eval("var_dump($args);");
}
?>
```

3. 代码审计与漏洞分析

3.1 逻辑分析

1. 代码使用 include 引入了 flag1.php，这意味着 flag 变量已经存在于当前脚本的全局作用域中。
2. 判断传入的 args 参数是否符合正则 /^\w+$/（只能包含字母、数字、下划线，且不能为空）。
3. 如果符合，就使用 eval("var_dump($args);"); 执行代码。注意这里 $args 被当作了变量名。

3.2 漏洞点

由于正则限制极其严格，无法使用括号、引号、分号等符号，常规的命令执行无法使用。
但 eval 允许我们执行任意代码，结合 PHP 的超全局变量（Superglobals）特性，我们可以构造 Payload 打印出所有的全局变量。

常用的超全局变量如 $GLOBALS 包含了所有的全局变量（包括 flag1.php 中的 $flag），且其变量名 GLOBALS 符合 \w+ 的规则。

4. 解题步骤

1. 构造 Payload：?args=GLOBALS
2. 传入 URL 后，代码变为执行 eval("var_dump($GLOBALS);");。
3. var_dump 会将所有全局变量打印出来。在输出结果的末尾，我们可以看到 ["ZFkwe3"]=> string(38) "flag{b225ae539655d66ff5db14b8bc126850}"。

注意：这里的 ZFkwe3 是 flag1.php 中存放 flag 的变量名，因环境而异，但通过 $GLOBALS 都可以直接打印出来。

5. 最终 Payload

```text
http://<靶机IP>/?args=GLOBALS
```

6. 总结与防御

· 漏洞成因: 开发者在使用 eval() 等危险函数时，未对输入进行充分过滤。虽然过滤了特殊字符，但未限制超全局变量的访问。
· 防御建议:
  1. 避免使用 eval()：尽量重构代码，不要使用执行动态代码的函数。
  2. **严格过滤
