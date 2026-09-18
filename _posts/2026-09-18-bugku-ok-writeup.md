---
title: 【Bugku CTF Writeup】Crypto-ok
date: 2026-09-18 12:00:00 +0800
tags: [CTF, Bugku, Writeup, Crypto, Ook, Brainfuck]
categories: 网络安全
---

## 一、题目信息

- **题目名称**：ok
- **题目类型**：Crypto（密码学/编码转换）
- **靶场来源**：Bugku CTF
- **考察知识点**：Ook! 深奥语言、标准 Ook! 与 Short Ook! 的区别、Brainfuck 虚拟机、Python 脚本编写

## 二、解题过程

### 1. 附件分析与编码识别
下载并打开附件 `file.txt`，发现内容全是由 `Ook.`、`Ook?` 和 `Ook!` 组成的字符块。
这是典型的 **Ook! 语言**。Ook! 是一种图灵完备的深奥编程语言（Esoteric Programming Language），它本质上是著名的 **Brainfuck** 语言的变体，将 Brainfuck 的符号替换为了猩猩语言风格的单词组合。

### 2. 踩坑与格式剖析
在解题过程中，直接使用 Bugku 自带的“Ook! To Text”工具可能会失败或输出乱码。
这是因为 Ook! 存在两种常见格式，极易混淆：

*   **Short Ook!**：单个词直接映射，例如 `Ook.` 等于 `>`，`Ook?` 等于 `<`，`Ook!` 等于 `+`。
*   **标准 Ook!**：必须**两个词**组合成一条指令，例如 `Ook. Ook?` 等于 `>`，`Ook! Ook!` 等于 `-`。

本题使用的是**标准 Ook! 格式**。如果像 Short Ook! 那样直接将文本中的 `Ook.` 批量替换为 `>`，会导致词组结构被彻底打乱，从而无法正确解码。

### 3. 编写脚本解密
最稳妥且最高效的解法是编写 Python 脚本，先将标准 Ook! 正确地转换为 Brainfuck 代码，然后再通过 Python 模拟 Brainfuck 虚拟机执行该代码，直接获取结果。

**核心映射表如下：**

| Ook! Token | Brainfuck 符号 |
| :--- | :--- |
| `Ook. Ook.` | `+` |
| `Ook. Ook?` | `>` |
| `Ook? Ook.` | `<` |
| `Ook! Ook!` | `-` |
| `Ook! Ook.` | `.` |
| `Ook. Ook!` | `,` |
| `Ook! Ook?` | `[` |
| `Ook? Ook!` | `]` |

**解密脚本 (solve.py)：**

```python
import os

def ook_to_brainfuck(ook_text):
    """将标准 Ook! 转换为 Brainfuck 代码"""
    tokens = ook_text.replace('\n', ' ').replace('\r', '').split()
    
    mapping = {
        'Ook. Ook.': '+', 'Ook. Ook?': '>', 'Ook? Ook.': '<',
        'Ook! Ook!': '-', 'Ook! Ook.': '.', 'Ook. Ook!': ',',
        'Ook! Ook?': '[', 'Ook? Ook!': ']'
    }
    
    bf_code = ""
    # 两个词一组进行遍历
    for i in range(0, len(tokens) - 1, 2):
        pair = tokens[i] + ' ' + tokens[i+1]
        if pair in mapping:
            bf_code += mapping[pair]
            
    return bf_code

def run_brainfuck(code):
    """执行 Brainfuck 代码并返回字符串输出"""
    code = ''.join([c for c in code if c in '><+-.,[]'])
    
    matching_brackets = {}
    stack = []
    for i, c in enumerate(code):
        if c == '[': stack.append(i)
        elif c == ']':
            start = stack.pop()
            matching_brackets[start] = i
            matching_brackets[i] = start

    tape = [0] * 30000
    ptr = 0
    ip = 0
    output = []
    
    while ip < len(code):
        cmd = code[ip]
        if cmd == '+': tape[ptr] = (tape[ptr] + 1) % 256
        elif cmd == '-': tape[ptr] = (tape[ptr] - 1) % 256
        elif cmd == '>': ptr += 1
        elif cmd == '<': ptr -= 1
        elif cmd == '.': output.append(chr(tape[ptr]))
        elif cmd == '[':
            if tape[ptr] == 0: ip = matching_brackets[ip]
        elif cmd == ']':
            if tape[ptr] != 0: ip = matching_brackets[ip]
        ip += 1
        
    return ''.join(output)

if __name__ == '__main__':
    # 读取同目录下的 file.txt
    file_path = os.path.join(os.path.dirname(__file__), 'file.txt')
    with open(file_path, 'r', encoding='utf-8') as f:
        ook_text = f.read()

    bf_code = ook_to_brainfuck(ook_text)
    result = run_brainfuck(bf_code)
    
    print("解密结果:")
    print(result)
