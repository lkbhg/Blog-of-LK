# TUN 模式接管问题排查与解决方案

## 背景
在使用Nekoray或者以sing-box 1.9.7为核心的代理工具时，存在TUN模式下更改配置后，出现“dns exchanged failed”等接管失效的问题。

并在开启TUN-IPv6 后，无法访问部分网站（如 ChatGPT），但 Google搜索等功能能正常访问。此时日志显示 `dial tcp6: network unreachable` 错误。

在排查过程中，将问题锁定在路由表回环错误，以及DNS域名选择不恰当等。

发现代理服务器通过域名订阅，且域名返回没有 IPv6 记录，导致 TUN 模式接管 IPv6 后无法正常工作。

---

## 1. 问题分析

### 问题症状

- **部分网站（如 ChatGPT）无法访问**
- **Google 搜索正常**
- **`dial tcp6: network unreachable` 错误**

### 路由表分析

IPv6 路由表显示了两个默认路由 `::/0`，分别指向 TUN 网卡和物理网卡。由于 TUN 网卡的 IPv6 默认路由没有指定网关，导致无法通过 TUN 正常转发 IPv6 流量。

### 服务器支持情况

通过 `nslookup` 查询代理服务器域名，返回：


*** No IPv6 address (AAAA) records available


这表明代理服务器的域名 **没有 IPv6 地址**，因此无法通过 IPv6 路由访问该服务器。

---

## 2. 解决方案

### 方案 A：关闭 TUN IPv6 接管

由于代理服务器不支持 IPv6，关闭 TUN 模式下的 IPv6 接管是最稳定的解决方案：

```json
"ipv6": false
```

这样，IPv6 流量将由系统默认处理，IPv4 流量将通过代理正常路由，避免了无法访问某些 IPv6 网站的问题。

### 方案 B：强制所有流量走 IPv4

在 sing-box 配置中添加以下设置，强制使用 IPv4：

```json
"domain_strategy": "prefer_ipv4"
```

此设置将确保所有流量使用 IPv4，无论 TUN 是否接管 IPv6。这样可以避免由于服务器不支持 IPv6 导致的错误。

### 方案 C：实现 IPv6 全接管（需要支持 IPv6 的代理服务器）

如果希望完全实现 IPv6 TUN 接管，代理服务器必须支持 IPv6。验证代理服务器是否支持 IPv6 的方法：

使用 nslookup -type=AAAA 命令检查域名是否有 IPv6 地址（AAAA 记录）。

使用 curl -6 命令验证是否能通过 IPv6 访问服务器。

如果服务器支持 IPv6，配置如下：
```json
{
  "type": "tun",
  "stack": "system",
  "auto_route": true,
  "strict_route": false,
  "ipv6": true,
  "inet4_address": "172.19.0.1/30",
  "inet6_address": "fdfe:dcba:9876::1/64"
}
```
确保删除原生 IPv6 默认路由，并让 sing-box 接管 IPv6 流量。

3. 核心问题

问题根本原因：代理服务器没有提供 IPv6 地址，导致 TUN 模式无法处理 IPv6 流量。

Windows IPv6 路由问题：即使在 TUN 模式下，Windows 默认使用多个 IPv6 默认路由，当路由链路不完整时，导致无法通过 TUN 转发 IPv6 流量。

解决方案：关闭 TUN 模式下的 IPv6 接管，或者使用 prefer_ipv4 强制流量走 IPv4，避免由于服务器不支持 IPv6 导致的访问失败。

4. 进一步验证代理服务器 IPv6 支持

查询域名是否有 AAAA 记录：

nslookup -type=AAAA 你的订阅域名

如果没有返回 IPv6 地址，则服务器不支持 IPv6。

验证通过 IPv6 是否可以访问：

curl -6 https://你的订阅域名

如果无法通过 IPv6 访问，说明服务器不支持 IPv6。

验证客户端是否支持 IPv6：

使用 curl -6 https://ip.sb 来测试客户端是否能正常使用 IPv6 出口。

5. 总结与建议

最推荐的方案：关闭 TUN 模式下的 IPv6 接管，使用默认的系统 IPv6 路由。

全接管 IPv6：需要确保代理服务器支持 IPv6 才能实现完全的 IPv6 TUN 接管。

如果代理不支持 IPv6，建议使用 prefer_ipv4 或关闭 TUN IPv6 模式。

6. 备注

TUN 模式下 IPv6 默认路由配置存在不稳定性，尤其是在 Windows 上。避免使用不完全的路由链。

在配置过程中，请确保 TUN 配置的网关和路由正确，否则会导致无法访问部分网站。
