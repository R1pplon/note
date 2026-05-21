# 添加静态 IPv6 地址

现在，你将修改现有的 `dummy0-static` 连接配置文件，以添加静态 IPv6 地址和网关。

## 任务

- 修改 `dummy0-static` 连接配置文件。
- 添加静态 IPv6 地址 `2001:db8:cafe::10/64`。
- 将 IPv6 网关设置为 `2001:db8:cafe::1`。
- 将更改应用到当前活动的连接。

## 要求

- 必须使用 `nmcli` 命令行工具修改现有连接。
- 更改必须应用到 `dummy0-static` 配置文件。

## 提示

要修改现有的 NetworkManager 连接，请使用 `nmcli connection modify` 命令：

- 使用 `ipv6.method manual` 启用手动 IPv6 配置。
- 使用 `ipv6.addresses` 设置 IPv6 地址。
- 使用 `ipv6.gateway` 设置 IPv6 网关。

基本语法如下：

```bash
sudo nmcli connection modify [CONNECTION_NAME] ipv6.method manual ipv6.addresses [ADDRESS/PREFIX] ipv6.gateway [GATEWAY]
```

修改连接配置文件后，必须重新激活它才能使更改生效：

```bash
sudo nmcli connection up [CONNECTION_NAME]
```

## 示例

应用更改后，`ip addr show dummy0` 的输出现在应同时包含 IPv4 和 IPv6 地址。

```plaintext
3: dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 12:34:56:78:9a:bc brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.10/24 brd 192.168.100.255 scope global noprefixroute dummy0-static
       valid_lft forever preferred_lft forever
    inet6 2001:db8:cafe::10/64 scope global noprefixroute
       valid_lft forever preferred_lft forever
    ...
```

你可以通过检查连接详情来验证 IPv6 配置是否已应用：

```plaintext
$ nmcli con show dummy0-static | grep ipv6
ipv6.method:                            manual
ipv6.addresses:                         2001:db8:cafe::10/64
ipv6.gateway:                           2001:db8:cafe::1
...
```
