# Bugku CTF - Crypto - ok

## 1. 题目信息
- **题目名称**：ok
- **题目类型**：Crypto / 编码转换
- **题目描述**：Ook.
- **附件**：`file.txt`
- **解题工具**：Python 3

## 2. 题目分析

下载附件 `file.txt`，打开后发现内容全是由 `Ook.`、`Ook?` 和 `Ook!` 组成的字符串。

这是一种名为 **Ook!** 的深奥编程语言，专为猩猩设计。它本质上是著名的 **Brainfuck** 语言的变体，使用两个单词的组合来代替 Brainfuck 的符号。

### 踩坑记录
直接使用在线工具（如 Bugku 自带的工具箱）解密时可能会失败，或者输出乱码。这是因为 Ook! 分为两种格式：
- **Short Ook!**：单个词直接对应一个符号，如 `Ook.` = `>`，`Ook?` = `<`。
- **标准 Ook!**：必须**两个词**组合才能对应一个指令，如 `Ook. Ook?` = `>`。

本题使用的是**标准 Ook! 格式**。如果像 Short Ook! 那样使用记事本将所有的 `Ook.` 替换为 `>`，会彻底破坏词组的关联性，导致解码失败。

## 3. 解题过程

针对标准 Ook!，我们需要先将两个词一组的 Token 转换为 Brainfuck 符号，然后再模拟 Brainfuck 虚拟机执行代码。

### 转换映射表
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

### 自动化脚本 (solve.py)
编写 Python 脚本自动读取 `file.txt`，将其转换为 Brainfuck 并执行，直接获取结果。

运行脚本：
```bash
python solve.py
