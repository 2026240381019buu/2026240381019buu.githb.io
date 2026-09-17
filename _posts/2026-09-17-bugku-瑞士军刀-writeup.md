---
title: 【Bugku CTF Writeup】PWN-瑞士军刀 (Swiss Army Knife)
date: 2026-09-17 22:00:00 +0800
tags: [CTF, Bugku, Writeup, PWN, nc]
categories: 网络安全
---

## 一、题目信息

- **题目名称**：瑞士军刀
- **题目类型**：PWN
- **靶场来源**：Bugku CTF
- **考察知识点**：nc (netcat) 工具的使用、Linux 基础命令（ls、cat）、PWN 入门环境搭建

## 二、解题过程

### 1. 题目分析
启动靶机后，页面会给出一个 IP 地址和端口号（例如 `114.67.246.176:13340`）。题目提示需要连接到该地址，这是一个典型的 PWN 入门题，考察的是使用 `nc` 连接远程服务并读取 flag 的能力。

### 2. 环境准备
由于需要用到 `nc` 命令，推荐在 **Kali Linux** 虚拟机中进行操作。
*如果还未安装 Kali，可以下载 VMware 虚拟机，并在官网下载 Kali 镜像进行安装。网络模式建议设置为桥接模式，以确保能连接到物理主机及外网。*

### 3. 连接靶机
打开 Kali 的终端，使用 `nc` 命令连接靶机。命令的基本格式为：
```bash
nc [host] [port]
