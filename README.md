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
