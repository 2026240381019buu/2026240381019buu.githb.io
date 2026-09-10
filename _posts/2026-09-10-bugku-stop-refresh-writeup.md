---
title: 【Bugku CTF Writeup】Web-你必须让他停下（JS断点/阻止刷新）
date: 2026-09-10 18:00:00
tags:
  - CTF
  - Bugku
  - Writeup
  - Web安全
categories:
  - 网络安全
---

## 一、题目信息
- **题目名称**：你必须让他停下
- **题目类型**：Web
- **靶场来源**：Bugku CTF

## 二、解题过程

**1. 启动环境，分析页面**
启动靶机后，页面上的文字提示是：
`I want to play Dummy game with others. But I can't stop! Stop at panda ! u will get flag`
网页在不停地闪烁和自动刷新，导致我们根本无法看清最终的页面。提示明确告诉我们要在 `panda` 处停下。

**2. 定位前端刷新代码**
遇到网页无限刷新（重定向）的情况，首先按 `F12` 打开开发者工具。
- 点击顶部的 **`源代码/来源`（Sources）** 标签页。
- 在左侧文件树中找到当前网页的 HTML 文件（通常叫 `(索引)` 或 `index`）。
- 观察页面的 JavaScript 代码，发现关键的一段：
  ```javascript
  function myrefresh(){
      window.location.reload();
  }
  setTimeout('myrefresh()', 500);
