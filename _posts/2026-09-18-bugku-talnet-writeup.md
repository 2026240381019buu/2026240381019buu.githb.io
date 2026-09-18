---
title: 【Bugku CTF Writeup】MISC-Telnet 流量分析
date: 2026-09-18 12:00:00 +0800
tags: [CTF, Bugku, Writeup, MISC, Wireshark, Telnet]
categories: 网络安全
---

## 一、题目信息

- **题目名称**：Telnet
- **题目类型**：MISC（杂项/流量分析）
- **靶场来源**：Bugku CTF
- **考察知识点**：Wireshark 基础使用、Telnet 明文传输特性、TCP 流追踪、十六进制搜索

## 二、解题过程

### 1. 附件分析
下载附件 `telnet.zip`，解压后得到 `networking.pcap` 流量包文件。

> 💡 **踩坑提示**：如果直接用记事本打开 `.pcap` 文件，会看到大量乱码。因为 pcap 是二进制文件，记事本无法正确解析网络协议格式，需要使用专业的流量分析工具。

### 2. 方法一：Wireshark 追踪流（推荐）
1. 使用 Wireshark 打开 `networking.pcap` 文件。
2. 在顶部过滤器栏输入 `telnet` 并按回车，过滤出 Telnet 协议的流量。
3. 右键点击任意一个数据包 -> 选择 **追踪流 (Follow)** -> **TCP 流 (TCP Stream)**。
4. 在弹出的窗口内容中，直接搜索或滚动查找 `flag` 关键字。

### 3. 方法二：PowerShell 命令行搜索（偷懒法）
如果不想下载 Wireshark，可以在该目录下打开 PowerShell，输入以下命令直接搜索：
```powershell
Select-String -Path .\networking.pcap -Pattern "flag"
