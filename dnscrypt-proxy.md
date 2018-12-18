灵活的DNS代理，支持现代加密DNS协议，如DNSCrypt v2和DNS-over-HTTPS。

# 特征

* DNS流量加密和身份验证。使用TLS 1.3和DNSCrypt支持DNS-over-HTTPS（DoH）。
* DNS查询监控，具有用于常规和可疑查询的单独日志文件
* 过滤：屏蔽广告，恶意软件和其他不需要的内容。兼容所有DNS服务
* 基于时间的过滤，具有灵活的每周计划
* 将特定域透明重定向到特定解析器
* DNS缓存，以减少延迟并提高隐私
* 本地IPv6阻止可减少仅IPv4网络的延迟
* 负载平衡：选择一组解析器，dnscrypt-proxy将自动测量并跟踪其速度，并在最快的可用速率之间平衡流量。
* 伪装：类似于HOSTS类固醇的文件，可以返回特定名称的预配置地址，或者解析并返回其他名称的IP地址。这可用于本地开发以及在* Google，Yahoo和Bing上实施安全搜索结果。
* 解析器列表的自动后台更新
* 可以强制传出连接使用TCP
* 支持SOCKS代理
* 与DNSSEC兼容

# 安装

前往 [DNSCrypt-Proxy](https://github.com/jedisct1/dnscrypt-proxy/releases) 项目下载二进制程序 `dnscrypt-proxy-linux_arm-2.0.19.tar.gz` 到树莓派并解压缩。

注意：使用 DNSCrypt-Proxy 前请先关闭 Clash 配置内的 DNS 查询，避免端口冲突。

# 配置

复制配置文件模板 `example-dnscrypt-proxy.toml` 为 `example-dnscrypt-proxy.toml` ，然后按需编辑 `example-dnscrypt-proxy.toml` 。

```
server_names = ['cloudflare', 'google'] # 使用 Cloudflare 与 Google DNS
listen_addresses = ['0.0.0.0:53'] # 在树莓派所有 IP 上监听 53 端口
force_tcp = true # 始终使用 TCP 方式连接 DNS 服务器
proxy = "socks5://127.0.0.1:7891" # 使用本机 Clash Socks5 代理连接 DNS 服务器
fallback_resolver = '114.114.114.114:53' # 后备 DNS 服务器，推荐设置国内任意 DNS 服务器
cache = true # 开启 DNS 查询缓存
```

# 运行

执行 `./dnscrypt-proxy` 运行 DNSCrypt-Proxy ， 测试配置无问题后运行 `./dnscrypt-proxy -service install` 将 DNSCrypt-Proxy 安装为系统服务。

* 启动 DNSCrypt-Proxy
```
./dnscrypt-proxy -service start
```
* 停止 DNSCrypt-Proxy
```
./dnscrypt-proxy -service stop
```
* 重启 DNSCrypt-Proxy
```
./dnscrypt-proxy -service restart
```
* 卸载 DNSCrypt-Proxy 服务
```
./dnscrypt-proxy -service uninstall
```
* 检查 DNS 解析
```
./dnscrypt-proxy -resolve example.com
```
