---
title: 【Bugku CTF Writeup】MISC-这是一张单纯的图片 (HTML实体编码解密)
date: 2026-09-14 15:00:00 +0800
tags:
  - CTF
  - Bugku
  - Writeup
  - MISC
  - 隐写术
categories:
  - 网络安全
---

## 一、题目信息

- **题目名称**：这是一张单纯的图片
- **题目类型**：MISC（杂项）
- **靶场来源**：Bugku CTF
- **考察知识点**：文件后缀伪装、HTML 实体编码（HTML Entities）解密、隐写术入门

## 二、解题过程


### 关键点说明：

1. **`---` 必须顶格**：文件第一行必须是 `---`，不能有空格。
2. **`title`**：文章标题，建议用英文引号包起来，避免中文冒号等符号导致 YAML 解析错误。
3. **`date`**：格式必须严格是 `YYYY-MM-DD HH:mm:ss +0800`，`+0800` 表示东八区。
4. **`tags`**：用缩进列表写，每个标签前加 `-`。
5. **`categories`**：同样用缩进列表，也可以直接写一行 `categories: 网络安全`。
6. **文件名**：建议和 `date` 对应，例如 `2026-09-14-bugku-这是一张单纯的图片-writeup.md`。

### 如果你想批量生成

如果你在写多篇 Bugku writeup，可以用这个通用模板：

```markdown
---
title: 【Bugku CTF Writeup】MISC-题目名称
date: 2026-09-14 15:00:00 +0800
tags:
  - CTF
  - Bugku
  - Writeup
  - MISC
categories:
  - 网络安全
---

## 一、题目信息

- **题目名称**：
- **题目类型**：
- **靶场来源**：Bugku CTF
- **考察知识点**：

## 二、解题过程

### 1. 观察题目

### 2. 分析线索

### 3. 解码/提取

## 三、最终答案

```text
flag{d3fcbf17f9399504}
