# 🚀 VLESS Reality 一键管理脚本

## 简要使用说明

### 介绍

一个 **基于 Xray + VLESS Reality 的一键安装与管理脚本**，支持安装、更新、卸载、配置查看，并提供 `vless` 全局命令进行长期管理。

适合：

* VPS 自建 Reality 节点
* 博客教程配套脚本
* 需要 **可重复安装 / 可恢复配置** 的用户

## 启动和配置

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/jinqians/vless/main/vless.sh)
```

如使用shadowrocket，直接扫码即可；

如使用clash verge，可配置`.yaml`文件格式如下：

```yaml
proxies:
  - name: "VLESS"
    type: vless
    server: "your ip address"
    port: 443
    uuid: "your uuid"
    network: tcp
    tls: true
    udp: true
    flow: xtls-rprx-vision
    servername: "your server name"
    reality-opts:
      public-key: "your public key"
      short-id: ""
    client-fingerprint: chrome
```

此外服务器防火墙需开放`443`端口，例如`Vultr`仅开放`22`端口

示例如下：

```bash
ufw allow 443/tcp
ufw reload
```

---

## ✨ 功能特性

* ✅ 一键安装 VLESS Reality（Xray）
* ✅ 自动生成 UUID / Reality Key
* ✅ 支持 IPv4 / IPv6
* ✅ 自动生成 **完整 VLESS Reality 链接**
* ✅ 生成二维码，便于手机扫码
* ✅ 安装完成后自动创建 `vless` 管理命令
* ✅ 菜单式管理（无需记参数）
* ✅ 支持更新 / 卸载 / 查看状态
* ✅ **配置可持久化保存（PublicKey 不丢失）**
* ✅ 可反复重装，不残留旧配置

---

## 📦 支持环境

* **系统**：

  * Debian 10+
  * Ubuntu 20.04+
* **架构**：

  * x86_64 / amd64
  * arm64
* **网络**：

  * IPv4
  * IPv6
  * 双栈

---

## 🚀 一键安装

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/jinqians/vless/main/vless.sh)
```

> 安装完成后，脚本会 **自动退出**，并提示你使用 `vless` 进行后续管理。

---

## 🚄 可选网络优化：启用 BBR

如果你的节点主要用于跨境访问、Cloudflare Tunnel、WordPress 后台管理，或承载 Xray / Shadowsocks 等代理流量，建议额外启用 **BBR**。

传统 TCP 拥塞控制通常依赖“丢包”来判断是否拥塞。在高延迟、轻微丢包的跨境线路上，这种策略很容易过度降速。例如 RTT 在 `150ms` 左右、丢包率只有 `1% - 3%` 时，也可能出现吞吐明显下降、窗口快速收缩的问题。

常见表现包括：

* WordPress 后台加载缓慢，打开媒体库或上传大文件容易超时
* 流媒体或代理连接在 `20Mbps` 左右开始抖动、掉速
* 低配机器 CPU 还没到瓶颈，网络吞吐已经先掉下来

BBR 会主动测量链路带宽与 RTT，而不是单纯依赖丢包推测拥塞，因此在高延迟链路上通常能获得更稳定的吞吐表现。对于大文件传输、备份同步、代理转发这类场景，往往会比默认算法更顺畅。

### 启用前先检查内核

先查看当前内核版本：

```bash
uname -r
```

建议使用 `5.4` 及以上内核。

然后确认系统是否支持 BBR：

```bash
sysctl net.ipv4.tcp_available_congestion_control
```

输出中应包含 `bbr` 或 `bbr2`。

如果云厂商提供的仍是较老的 `4.x` 内核，建议先升级到较新的内核后再启用。例如 Ubuntu 22.04 可安装：

```bash
sudo apt update
sudo apt install -y linux-image-generic-hwe-22.04
```

安装完成后重启系统，再继续下面的配置。

### Ubuntu / Debian 开启 BBR

以下步骤适用于 **Ubuntu 22.04**、**Debian 12** 等较新的发行版：

```bash
sudo apt update && sudo apt install -y --no-install-recommends ca-certificates

# 1. 确保系统已加载 tcp_bbr 模块
sudo modprobe tcp_bbr

# 2. 写入 sysctl 配置
cat <<'CFG' | sudo tee /etc/sysctl.d/90-bbr.conf
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
CFG

# 3. 应用并验证
sudo sysctl --system
sysctl net.ipv4.tcp_congestion_control
```

如果最后一行返回 `bbr`，说明已经启用成功。

> 提示：如果老旧内核不支持 `fq` 队列，可以临时改用 `fq_codel`；但从稳定性和兼容性考虑，仍然更建议优先升级内核。

---

## 🧭 管理方式（推荐）

安装完成后，直接使用：

```bash
vless
```

即可进入 **VLESS Reality 管理菜单**。

---

## 📋 管理菜单说明

进入 `vless` 后，可看到如下功能（以实际版本为准）：

* **安装 VLESS Reality**
* **更新 Xray**
* **卸载（彻底清理）**
* **查看运行状态**
* **查看当前配置**

  * UUID
  * PublicKey
  * 端口
  * serverNames
  * IPv4 / IPv6
  * 完整 VLESS Reality 链接（含 `pbk`）
* **更新脚本**

---

## 📄 配置文件说明

### Xray 配置文件

```text
/usr/local/etc/xray/config.json
```

### Reality 元信息文件（关键）

```text
/usr/local/etc/xray/vless-meta.conf
```

该文件用于保存：

* UUID
* Reality PublicKey
* 端口
* serverNames
* IPv4 / IPv6
* 安装时间

> ⚠️ **Reality 的 PublicKey 无法从服务端配置反推**
> 因此脚本会在安装时自动保存 PublicKey，用于后续配置查看与节点恢复。

---

## 🔐 客户端示例（VLESS Reality）

```text
vless://UUID@IP:PORT
?encryption=none
&flow=xtls-rprx-vision
&security=reality
&sni=example.com
&fp=chrome
&pbk=PUBLIC_KEY
&type=tcp
```

脚本会自动生成 **IPv4 / IPv6 完整链接**，无需手动拼接。

---

## ❌ 卸载说明

在管理菜单中选择 **卸载** 即可：

* 停止并禁用 Xray
* 删除所有配置文件
* 删除 systemd 服务
* 删除 `vless` 管理命令

卸载后 **不会残留任何旧配置**，可安全重新安装。

---

## 🧠 设计说明（重要）

* Reality 的 **PrivateKey 只存在于服务端**
* Reality 的 **PublicKey 只用于客户端**
* PublicKey **无法从服务端反推**
* 本脚本遵循 Reality 设计原则，仅在生成时保存 PublicKey，不进行任何破解或反推行为

---

## 🔄 更新脚本

进入管理菜单，选择：

```text
更新脚本
```

即可拉取最新版本并自动替换。

---

## 📌 常见问题

### Q：VPS 重启后还能找回配置吗？

A：可以，使用 `vless → 查看当前配置` 即可完整恢复。

---

### Q：可以多次安装吗？

A：可以，卸载后可重新安装，不会复用旧 UUID / Key。

---

### Q：支持 Clash / sing-box 吗？

A：当前脚本输出的是标准 VLESS Reality 链接，后续版本将支持直接导出 Clash Meta / sing-box 配置。

如使用clash verge，可配置`.yaml`文件格式如下：

```yaml
proxies:
  - name: "VLESS"
    type: vless
    server: "your ip address"
    port: 443
    uuid: "your uuid"
    network: tcp
    tls: true
    udp: true
    flow: xtls-rprx-vision
    servername: "your server name"
    reality-opts:
      public-key: "your public key"
      short-id: ""
    client-fingerprint: chrome
```

此外服务器防火墙需开放`443`端口，例如`Vultr`仅开放`22`端口

示例如下：

```bash
ufw allow 443/tcp
ufw reload
```



---

## 📄 License

MIT License
自由使用、修改与分发。

---

## 🤝 贡献 & 反馈

* 博客：[https://jinqians.com](https://jinqians.com)
* 欢迎 issue / PR
* 如果你在使用中有改进建议，也欢迎交流
