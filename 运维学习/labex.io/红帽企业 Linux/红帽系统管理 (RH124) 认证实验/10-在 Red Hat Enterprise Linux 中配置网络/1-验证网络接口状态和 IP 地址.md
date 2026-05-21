# 验证网络接口状态和 IP 地址

在这一步中，你将学习如何使用命令行工具验证 Red Hat Enterprise Linux 系统上的网络接口状态和 IP 地址。了解你的网络配置对于排除连接问题和管理网络服务至关重要。

首先，让我们探索 `ip link` 命令，该命令列出系统上所有可用的网络接口。此命令提供了网络适配器的高级概述，包括它们的状态（UP/DOWN）、MAC 地址和 MTU（最大传输单元）。

打开你的终端。你应该会看到类似于 `[labex@host ~]$` 的提示符。

```bash
ip link show
```

你将看到类似这样的输出，显示诸如 `lo`（环回）、`eth0` 和 `eth1`（以太网接口，具有备用名称）之类的接口：

```plaintext
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:16:3e:0f:9e:4e brd ff:ff:ff:ff:ff:ff
    altname enp0s6
    altname ens6
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:16:3e:0f:9e:51 brd ff:ff:ff:ff:ff:ff
    altname enp0s7
    altname ens7
```

请注意，你的系统有两个以太网接口（`eth0` 和 `eth1`），具有备用名称（分别为 `enp0s6`/`ens6` 和 `enp0s7`/`ens7`）。`qdisc mq` 表示正在使用多队列网络调度程序以获得更好的性能。

接下来，我们将使用 `ip addr` 命令来查看特定网络接口的详细设备和地址信息。此命令提供有关已分配的 IP 地址（IPv4 和 IPv6）、广播地址和子网掩码的信息。

让我们检查你的 `eth0` 接口的详细信息：

```bash
ip addr show eth0
```

输出将显示分配给 `eth0` 的 IP 地址，包括 IPv4 和 IPv6 地址（如果已配置）：

```plaintext
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:16:3e:0f:9e:4e brd ff:ff:ff:ff:ff:ff
    altname enp0s6
    altname ens6
    inet 172.16.50.116/24 brd 172.16.50.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::216:3eff:fe0f:9e4e/64 scope link
       valid_lft forever preferred_lft forever
```

请注意，`eth0` 的 IP 地址为 `172.16.50.116/24`，带有 `noprefixroute` 标志，这表明 NetworkManager 正在管理此接口的路由。

`ip -s link show` 命令还可以显示有关网络性能的统计信息，例如已发送和接收的字节数和数据包数，以及任何错误或丢弃的数据包。这对于快速检查网络流量很有用。

```bash
ip -s link show eth0
```

你将看到 `eth0` 接口的统计信息：

```plaintext
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:16:3e:0f:9e:4e brd ff:ff:ff:ff:ff:ff
    RX:  bytes packets errors dropped  missed   mcast
         90512     884      0       0       0       0
    TX:  bytes packets errors dropped carrier collsns
       1430185    1069      0       0       0       0
    altname enp0s6
    altname ens6
```

最后，让我们使用 `ip route` 命令验证路由表。路由表确定网络流量如何定向到其目的地。

```bash
ip route
```

此命令将显示 IPv4 路由表，显示默认路由和特定网络的路由。由于你有两个网络接口，你将看到多个路由：

```plaintext
default via 172.16.50.253 dev eth0 proto dhcp src 172.16.50.116 metric 100
default via 172.16.50.253 dev eth1 proto dhcp src 172.16.50.117 metric 200
172.16.50.0/24 dev eth0 proto kernel scope link src 172.16.50.116 metric 100
172.16.50.0/24 dev eth1 proto kernel scope link src 172.16.50.117 metric 200
```

请注意，有两个具有不同指标（100 和 200）的默认路由，这意味着 `eth0` 由于其较低的指标值而具有优先级。两个接口都连接到同一网段（172.16.50.0/24）并使用相同的网关（172.16.50.253）。`eth0` 接口的 IP 地址为 `172.16.50.116/24`，`eth1` 的 IP 地址为 `172.16.50.117/24`。

要查看 IPv6 路由表，请使用 `ip -6 route` 命令：

```bash
ip -6 route
```

你将看到两个接口的 IPv6 路由条目：

```plaintext
::1 dev lo proto kernel metric 256 pref medium
fe80::/64 dev eth0 proto kernel metric 256 pref medium
fe80::/64 dev eth1 proto kernel metric 256 pref medium
```
