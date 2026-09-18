# Bugku CTF - Telnet 流量分析 Writeup

## 📌 题目信息
- **平台**：Bugku CTF
- **分类**：杂项 (Misc) / 流量分析
- **题目名称**：Telnet
- **附件**：`telnet.zip`

## 🛠️ 解题工具
- [Wireshark](https://www.wireshark.org/) (核心工具)
- 记事本 / 010 Editor / WinHex (辅助工具)

## 🚀 解题过程

### 1. 附件分析
下载附件 `telnet.zip` 并解压，得到一个名为 `networking.pcap` 的流量包文件。

> 💡 **踩坑提示**：如果直接用记事本打开 `.pcap` 文件，会看到大量乱码。因为 pcap 是二进制文件，记事本无法正确解析网络协议格式。

### 2. Wireshark 追踪流（标准解法）
1. 使用 Wireshark 打开 `networking.pcap` 文件。
2. 在顶部过滤器栏输入 `telnet` 并回车，筛选出 Telnet 协议的流量。
3. 右键点击任意一个数据包 -> 选择 **追踪流 (Follow)** -> **TCP 流 (TCP Stream)**。
4. 在弹出的窗口中，寻找 `flag` 关键字。

> 💡 **备选方案**：如果习惯使用命令行，可以在该目录下打开 PowerShell，输入：
> ```powershell
> Select-String -Path .\networking.pcap -Pattern "flag"
