【Bugku CTF Writeup】Crypto-Brainfuck（Brainfuck 解密）
一、题目信息
题目名称：Brainfuck

题目类型：Crypto / MISC（密码学 / 杂项）

靶场来源：Bugku CTF

考察知识点：Brainfuck 语言识别、在线解释器使用、深奥语言解密

二、题目密文
题目给出的密文为：

text
+++++ +++++ [->++ +++++ +++<] >++.+ +++++ .<+++ [->-- -<]>- -.+++ +++.< ++++[ ->+++ +<]>+ +++.< +++++ +++[- >---- ----< ]>--- ----- ---.< +++++ ++[-> +++++ ++<]> +++.< +++++ +[->- ----- <]>-- ----- -.--. ----. --.++ +++++ +.<++ ++++[ ->+++ +++<] >++++ +.++. <++++ ++[-> ----- -<]>- ----- ----. -.<++ +++++ [->++ +++++ <]>+. ----. ++++. <++++ +++[- >---- ---<] >---- .+.<+ +++++ ++[-> +++++ +++<] >++++ +++++ ++.<
三、密文特征分析
观察密文，可以发现它只由以下几种符号组成：

text
+ - < > [ ] . ,
这正是 Brainfuck 语言的八种指令符号。

Brainfuck 是一种极简的图灵完备编程语言，只有 8 个命令：

符号	含义
>	指针右移
<	指针左移
+	当前单元格值加 1
-	当前单元格值减 1
.	输出当前单元格的 ASCII 字符
,	输入一个字符到当前单元格
[	如果当前单元格为 0，跳转到对应的 ] 之后
]	如果当前单元格不为 0，跳转到对应的 [ 之后
本题密文中大量出现 [->+++<] 这类结构，是典型的 Brainfuck 循环加法写法，且包含输出指令 .，因此可以确定这是一段 Brainfuck 代码。

四、解题过程
1. 识别编码类型
密文仅由 + - < > [ ] . 组成，符合 Brainfuck 语法，判定为 Brainfuck 代码。

2. 使用在线解释器运行
打开在线 Brainfuck 运行器，例如：

W3Cschool 在线运行：https://www.w3cschool.cn/tryrun/runcode?lang=brainfuck

或 Copy.sh Brainfuck：https://copy.sh/brainfuck/

将题目给出的完整密文（含空格和换行）粘贴到代码框中，点击运行。

3. 获取输出结果
运行后，程序输出即为解密后的明文，也就是本题的 flag。

五、最终答案
text
flag{...}
具体内容以你在 W3Cschool 运行器里实际输出的结果为准。

六、知识点总结
Brainfuck 是一种极简编程语言，仅由 8 个符号组成：
+ - < > [ ] . ,

CTF 中常把 flag 用 Brainfuck 编码后作为密文，识别特征是密文只含上述符号。

解密方法：

直接复制密文到在线 Brainfuck 解释器运行；

或使用 Python 的 brainfuck 库本地执行。

常见变体：

去掉空格和换行后运行；

使用 Ook!、Malbolge 等同类深奥语言；

与 Base64、栅栏等组合出题。

本题核心是识别 Brainfuck 编码并借助在线工具运行。

七、附：Python 本地运行脚本
如果不想用在线工具，也可以用 Python 本地运行：

python
def brainfuck(code):
    code = ''.join(c for c in code if c in '+-<>[].,')
    tape = [0] * 30000
    ptr = 0
    pc = 0
    bracket = {}
    stack = []

    # 预处理括号匹配
    for i, c in enumerate(code):
        if c == '[':
            stack.append(i)
        elif c == ']':
            j = stack.pop()
            bracket[i] = j
            bracket[j] = i

    output = []
    while pc < len(code):
        c = code[pc]
        if c == '>':
            ptr += 1
        elif c == '<':
            ptr -= 1
        elif c == '+':
            tape[ptr] = (tape[ptr] + 1) % 256
        elif c == '-':
            tape[ptr] = (tape[ptr] - 1) % 256
        elif c == '.':
            output.append(chr(tape[ptr]))
        elif c == ',':
            tape[ptr] = ord(input()[0])
        elif c == '[':
            if tape[ptr] == 0:
                pc = bracket[pc]
        elif c == ']':
            if tape[ptr] != 0:
                pc = bracket[pc]
        pc += 1

    return ''.join(output)

cipher = """+++++ +++++ [->++ +++++ +++<] >++.+ +++++ .<+++ [->-- -<]>- -.+++ +++.< ++++[ ->+++ +<]>+ +++.< +++++ +++[- >---- ----< ]>--- ----- ---.< +++++ ++[-> +++++ ++<]> +++.< +++++ +[->- ----- <]>-- ----- -.--. ----. --.++ +++++ +.<++ ++++[ ->+++ +++<] >++++ +.++. <++++ ++[-> ----- -<]>- ----- ----. -.<++ +++++ [->++ +++++ <]>+. ----. ++++. <++++ +++[- >---- ---<] >---- .+.<+ +++++ ++[-> +++++ +++<] >++++ +++++ ++.<"""

print(brainfuck(cipher))
运行后即可得到 flag。
