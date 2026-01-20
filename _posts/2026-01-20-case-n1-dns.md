layout: post
title: "Case N1 | 能 ping IP 但打不开域名（DNS）"
categories: [A+220-1201, Network]
tags: [DNS, Troubleshooting, Objective-2-1]
------

Case N1-01 | 能 ping IP, 但打不开网战
### 📌 症状（用户语言）
“能连通IP 地址，但基于域名的访问全部失败。”

### 🧠 初始判断（Hypothesis Tree）
- DNS 解析失败
- 默认网关异常
- 浏览器/代理
- 防火墙/VPN
- ISP问题（低概率）

### 🔍 验证过程
Step 1｜ping 8.8.8.8 验证IP连通性
- 结果：![[Screenshot 2026-01-19 at 9.50.55 PM.png]]
- 结论：排除本地物理连接与基础外网连通性问题，ISP层问题可能性降低但未完全排除

Step 2｜ping google.com，验证DNS
- 结果：失败![[Screenshot 2026-01-19 at 10.03.37 PM.png]]
- 结论：DNS高度可疑

Step 3｜nslookup google.com, 直接查询DNS
- 结果：timeout/ no server found
- ![[Screenshot 2026-01-19 at 10.02.49 PM.png]]
- 结论：DNS的问题，nslookup 直接失败，说明问题发生在DNS 查询阶段之前，而不是解析结果错误(如 NXDOMAIN)

### 🛠 修复动作
- 做了什么（具体到命令/设置）：scutil --dns，用于确认当前系统实际使用的DNS server，而不是仅看网络面板显示值
![[Screenshot 2026-01-19 at 10.33.05 PM.png]]
* ### Evidence｜scutil --dns（验证系统实际生效的 DNS）

关键输出位于：DNS configuration (for scoped queries)

- resolver #1：
  - if_index: 11 (en0)
    - 说明：该 DNS 配置绑定在 Wi-Fi 接口 en0（接口级解析器）
  - nameserver[0]: 8.8.8.8
  - nameserver[1]: 1.1.1.1
    - 说明：系统实际用于 DNS 查询的服务器已变更为 Google/Cloudflare
  - flags: Scoped, Request A records, Request AAAA records
    - 说明：Scoped 表示“仅对该接口范围内的查询生效”；A/AAAA 表示允许查询 IPv4/IPv6 记录
  - reach: 0x00000002 (Reachable)
    - 说明：系统判断该 DNS 服务器当前可达（配置已生效且连通性正常）
  - search domain[0]: mynetworksettings.com
    - 说明：为搜索域（用于补全短主机名的解析），不影响 FQDN 如 google.com 的解析结论

补充：
- 输出中出现的其它 resolver（如 domain 为 *.ip6.arpa、options 为 mdns）
  多用于 IPv6 反向解析或本地域（Bonjour/mDNS），不代表公网 DNS 主路径 。这段注释的价值是：  
**把“我设置了 DNS”升级为“系统确实在 en0 上使用这组 DNS，并且可达”。**  面试官/审核者一眼就能信

----

* 手动设置DSN为:
	* 8.8.8.8
	* 1.1.1.1![[Screenshot 2026-01-19 at 10.07.56 PM.png]]
### ✅ 结果确认
- 修复后发生了什么：重新测试 ping google.com
- 网站可正常访问
- 问题解决
![[Screenshot 2026-01-19 at 10.09.10 PM.png]]
### 🔁 复盘（一句话规则）
> 能ping IP但不能解析域名→优先检查DNS

### ⚠️ 边界与反例
- DNS 正常但问题仍在 → 检查代理 / VPN → 防火墙 → hosts 文件

----
## 面试 2 分钟版


> “这是一个典型的网络排障案例。  
> 用户反馈‘能上网但打不开网站’，我首先没有下结论，而是做了假设分类。
> 
> 第一步我 ping 8.8.8.8，确认 IP 层连通，说明本地网络和外网基础是通的。  
> 第二步 ping 域名失败，于是我把问题范围缩小到 DNS。
> 
> 用 nslookup 验证后确认 DNS 超时，然后通过 scutil 检查系统实际 DNS 配置，发现异常。  
> 手动设置公共 DNS 后问题立刻解决。
> 
> 我的复盘规则是：  
> **能 ping IP 但不能解析域名，优先检查 DNS；如果 DNS 正常，再查代理、防火墙和 hosts。**”