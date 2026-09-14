---
layout: post
title: "2026-09-14 Bugku CTF 「矛盾」- PHP 弱类型比较绕过 Writeup"
date: 2026-09-14 15:00:00 +0800
categories: CTF Writeup
tags: [Bugku, PHP, 弱类型, Writeup]
---

# Bugku CTF - PHP 弱类型比较 (Web) Writeup

## 1. 题目信息
...
- **平台**: Bugku CTF
- **题目类型**: Web / PHP 代码审计
- **考点**: PHP 弱类型比较 (`==`)、`is_numeric()` 函数的绕过

## 2. 题目源码
访问题目环境，页面直接给出了 PHP 源码：

```php
$num=$_GET['num'];
if(is_numeric($num))
{
echo $num;
if($num==1)
echo 'flag{**********}';
}
```

3. 代码审计与漏洞分析

3.1 逻辑分析

我们需要满足两个条件才能拿到 flag：

1. 传入的 num 参数必须通过 is_numeric() 检查（即被认为是数字或数字字符串）。
2. 传入的 num 参数在进行弱比较时，必须等于 1 ($num == 1)。

3.2 漏洞点

这道题的核心在于 PHP 的 弱类型比较（Loose Comparison） 和 is_numeric() 函数的特性。

· is_numeric() 的特性: 该函数用于判断变量是否为数字或数字字符串。在 PHP 中，它允许数字字符串前后包含空格、换行符等空白字符（例如 " 1" 或 "1 " 都会返回 true）。
· == 弱比较的特性: 当字符串与数字使用 == 进行比较时，PHP 会尝试将字符串转换为数字。如果字符串以数字开头，PHP 会截取开头的数字部分进行转换，忽略后面的非数字字符（例如 "1abc" 会被转换为 1）。

4. 解题步骤

方法一：利用空格绕过（最推荐）

构造 Payload：?num=1  (注意 1 后面有一个空格，在 URL 中通常编码为 %20)

1. 测试 is_numeric()：
   传入 num=1 （或者 num=1%20），is_numeric("1 ") 会返回 True，因为空格是合法的数字前后缀。代码执行 echo $num;，输出 1 。
2. 测试弱比较 ==：
   执行 if($num==1)，PHP 将字符串 "1 " 转换为数字 1，条件成立。
3. 获取 Flag：
   成功满足所有条件，页面输出 flag{**********}。

方法二：利用科学计数法绕过

构造 Payload：?num=1e0

1. 测试 is_numeric()：is_numeric("1e0") 返回 True。
2. 测试弱比较 ==：在 PHP 中，"1e0" == 1 成立，因为 1e0 就是科学计数法表示的 1。
3. 同样可以拿到 Flag。

方法三：利用其他特殊字符（视 PHP 版本而定）

· ?num=1.0
· ?num=0x1 (旧版本 PHP 可能适用)
· ?num=1%0a (换行符)

5. 最终 Payload

```text
http://<靶机IP>/?num=1 
```

或者 URL 编码形式：

```text
http://<靶机IP>/?num=1%20
```

6. 总结与防御

· 漏洞成因: 开发者在进行数值判断时，过度依赖了 PHP 的弱类型比较（==），且未对输入进行严格过滤。
· 防御建议:
  1. 在需要进行严格的数字比较时，使用全等比较符 === 代替 ==。=== 会同时比较值和类型，"1 " === 1 会返回 false。
  2. 使用 intval() 或 filter_var($num, FILTER_VALIDATE_INT) 强制转换和验证输入。
  3. 如果只需要整数，严格检查输入是否只包含数字字符（例如使用正则 preg_match('/^[0-9]+$/', $num)）。

---

Author: [你的GitHub用户名]
Date: 2024-05-XX

```

### 提示：
*   记得把 `<靶机IP>` 替换成实际题目的 IP 地址。
*   把 `[你的GitHub用户名]` 和日期替换成你自己的信息。
*   在 GitHub 上渲染时，Markdown 会生成非常漂亮的排版。你可以直接创建一个名为 `Bugku-PHP-Weak-Type` 的仓库，把这段代码放进去。
