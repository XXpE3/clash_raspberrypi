# 特征

使用一个简单的 Raspberry Pi 设备当作局域网 代理 / 网关，供给局域网内的其他设备使用。

代理程序使用 Clash ， 可以针对多个代理服务器创建复杂的规则，例如：

* 可以将代理分组使用并指定规则
* 可以根据 GeoIP 实现不同区域使用不同地区代理服务器
* 日本 IP 通过 日本代理服务器
* Pixiv 与 DMM 通过 日本代理服务器
* Steam 与 Netflix 通过 香港代理服务器
* 默认外国 IP 通过 美国代理服务器

完整的介绍与规则使用请查阅 Clash 项目。

[Clash](https://github.com/Dreamacro/clash) A rule-based tunnel in Go.

# 安装

## Clash

下载编译 Clash：
```
go get -u -v github.com/Dreamacro/clash
```

或下载我编译好的：

[Clash for ARMv6L](https://github.com/gs002/clash_raspberrypi/releases)

## 配置

基础配置：
```
port: 7890
socks-port: 7891
redir-port: 7892
allow-lan: true
mode: Rule
log-level: info
external-controller: '127.0.0.1:9000'
secret: ''
dns:
   enable: true
   ipv6: false
   listen: 0.0.0.0:53
   enhanced-mode: redir-host
   nameserver:
     - tls://dns.rubyfish.cn:853
   fallback:
     - tls://1.1.1.1:853
     - tls://1.0.0.1:853
```
在 Raspberry Pi 上需要以 root 权限启动 Clash ，否则无法启用 53 端口监听。

然后按照 [Clash README](https://github.com/Dreamacro/clash/blob/master/README.md) 添加代理服务器跟配置规则。

## 转发

编辑修改 `/etc/sysctl.conf` 开启 `net.ipv4.ip_forward = 1` ，然后使用 `sysctl -p` 命令刷新生效。

```
iptables -t nat -N CLASH
iptables -t mangle -N CLASH

iptables -t nat -A CLASH -d x.x.x.x(树莓派局域网IP) -j RETURN

iptables -t nat -A CLASH -d 0.0.0.0/8 -j RETURN
iptables -t nat -A CLASH -d 10.0.0.0/8 -j RETURN
iptables -t nat -A CLASH -d 127.0.0.0/8 -j RETURN
iptables -t nat -A CLASH -d 169.254.0.0/16 -j RETURN
iptables -t nat -A CLASH -d 172.16.0.0/12 -j RETURN
iptables -t nat -A CLASH -d 192.168.0.0/16 -j RETURN
iptables -t nat -A CLASH -d 224.0.0.0/4 -j RETURN
iptables -t nat -A CLASH -d 240.0.0.0/4 -j RETURN

iptables -t nat -A CLASH -p tcp -j REDIRECT --to-ports 7892

ip route add local default dev lo table 100
ip rule add fwmark 1 lookup 100

iptables -t nat -A PREROUTING -p tcp -j CLASH
iptables -t mangle -A PREROUTING -j CLASH
```

# 使用

将其他局域网设备的网关与 DNS 服务器地址设置为 Raspberry Pi 地址即可。
