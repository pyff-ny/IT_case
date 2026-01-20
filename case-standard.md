下面是一页可直接放进你仓库（建议路径：`README.md` 或 `case-standard.md`）的 **「Case 规范（截图与证据标准）」**。目标是：每篇 Case 都能做到“面试官一眼看懂 + 可复现 + 可公开”。
from ChatGPT 5.2
---

# Case 规范（Network / Hardware / OS）

## 0) 适用范围

* **Network**：连通性、DNS/DHCP/Gateway、Wi-Fi、端口/服务、共享与发现（Bonjour/UPnP）
* **Hardware**：打印机、存储、性能（CPU/RAM/SSD）、外设、接口与驱动
* **OS**：macOS/Windows 操作系统层故障排除、服务/权限/配置、脚本与自动化

---

## 1) 统一结构（每篇 Case 必须有的 7 段）

1. **症状（用户语言）**：一句话/两句话，避免术语堆叠
2. **初始判断（Hypothesis Tree）**：3–6 条备选原因（带优先级）
3. **验证过程（Evidence）**：Step 1/2/3… 每步=命令/操作 + 结果
4. **根因（Root Cause）**：一句话定性 + 关键证据指向
5. **修复（Fix）**：明确动作 + 为什么有效
6. **复盘（Prevention）**：下次如何更快定位/避免复发
7. **映射（Objective/Skill）**：A+ objective 或技能点（可选但强烈建议）

> 标准：每一步验证都要能回答“所以呢？”——要么排除一个假设，要么确认一个假设。

---

## 2) 截图与证据的“硬标准”

### 2.1 截图必须满足

* **只截证据，不截聊天/桌面杂物**（裁剪到命令输出或设置界面核心区域）
* **可复现**：截图旁必须有命令/路径/操作步骤
* **可公开**：遵守脱敏规则（见 2.2）

### 2.2 脱敏规则（公开作品集默认执行）

强制打码/裁掉：

* 终端提示符中的 **用户名/主机名**（如 `user@mac ~ %`）
* **IP / SSID / VPN / 域名**（若涉及公司/家庭网络细节）
* **MAC 地址**（至少遮挡后 2–3 组；蓝牙/网卡都算）
* 任何 Token/Key/密码/序列号/共享路径/远端主机
* 完整用户路径（如 `/Users/<name>/...`）若无必要则裁掉用户名部分

推荐做法：

* 终端截图：**裁掉最上方提示符**；或截图前临时 `export PS1="$ "`
* 网络/蓝牙：MAC 地址打码；内网 IP 若非必要只保留网段（如 `192.168.x.x`）

---

## 3) 三类 Case 的“证据清单”（最低配）

### 3.1 Network（连通性类）

每个网络 Case 至少提供以下 3 类证据（能覆盖 80% A+ 题型）：

**A. IP 层连通性**

* `ping 8.8.8.8`（或网关/已知 IP）
* `ipconfig getifaddr en0` / `ifconfig`（macOS）
* 结果：能否出网、丢包、延迟

**B. DNS 解析**

* `nslookup google.com` 或 `dig google.com`
* macOS：`scutil --dns | head -n 60`
* 结果：是否能解析、用的哪台 DNS

**C. 路由/网关**

* `netstat -rn | head` 或 `route -n get default`（macOS）
* 结果：默认网关是否存在、路由是否异常

可选增强（出现 Wi-Fi/发现类问题时）：

* `airport -I`（Wi-Fi 信号/信道）
* 端口：`nc -vz host 443` / `curl -I https://...`
* 发现：Bonjour/UPnP（`dns-sd` / SSDP 相关）

---

### 3.2 Hardware（打印机/存储/性能）

最低配证据（按类型选 2–3 个）：

**打印机**

* macOS：System Settings → Printers & Scanners 截图（队列/状态）
* `lpstat -p -d`、`lpstat -t`
* 若网络打印：补一个 `ping printer_ip` + `nc -vz printer_ip 9100/631`

**存储/空间**

* `df -h`
* `du -hd 1 <path>`（必要时）
* 结果：空间瓶颈在哪、是否可清理/迁移

**性能（CPU/RAM）**

* Activity Monitor 截图（CPU/Memory/Swap）
* 终端可选：`top -o cpu` / `vm_stat`
* 结果：瓶颈是 CPU、内存还是 I/O

---

### 3.3 OS（服务/设置/权限/自动化）

最低配证据（选 2–4 个）：

**系统信息/硬件存在性**

* `system_profiler <Type> | head -n 30`（如 SPBluetoothDataType）
* 结果：控制器/驱动是否存在、状态是否正常

**服务/进程层**

* `ps aux | grep <service>`（或活动监视器）
* macOS 常见：`sudo pkill <daemon>` 作为恢复动作（需解释为何）

**设置/配置层**

* System Settings 关键页截图（裁掉个人信息）
* 结果：开关是否可操作、是否被策略/权限限制

**重置/恢复（仅当必要且有证据）**

* NVRAM/SMC（Intel 机型）
* 安全模式/重启验证
* 结果：复现率是否下降

---

## 4) 证据写作格式（统一模板）

每一步验证都用这个格式：

* **Step X | 做什么（目的）**

  * 命令/操作：`...`
  * 结果：……（贴关键一行或截图）
  * 结论：排除/确认了哪个假设

> 通过“结论”把证据转成判断，这是面试官最看的点。

---

## 5) 质量门槛（发布前自检）

发布前问自己 5 个问题（任一项为“否”，就补证据）：

1. 读者能否在 30 秒内看懂“问题是什么”？
2. 每一步是否都在排除/确认一个假设？
3. 根因是否有至少 1 条直接证据支撑？
4. 修复动作是否可复现且解释了为什么有效？
5. 截图是否完成脱敏（用户名/主机名/MAC/IP/密钥）？

---

## 6) 推荐命名规范（可选但利于长期维护）

* `case_id`：`N1-01 / H1-01 / OS2-01 / U1-01`
* 文件名：`YYYY-MM-DD-case-<domain>-<slug>.md`
* Title：`Case <ID> - <一句话症状/主题>`

---

### English summary

This is a one-page “Case Standard” for your portfolio: consistent 7-section structure, hard screenshot/redaction rules, and minimum evidence checklists for Network/Hardware/OS cases (commands + what each proves). It also includes a step format and a publish-quality checklist to keep every case interview-ready.
