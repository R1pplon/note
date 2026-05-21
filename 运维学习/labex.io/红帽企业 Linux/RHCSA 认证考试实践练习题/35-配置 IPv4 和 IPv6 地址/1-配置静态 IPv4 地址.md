# 配置静态 IPv4 地址

你的第一个任务是为 `dummy0` 接口创建一个新的、持久的 NetworkManager 连接配置文件，并进行静态 IPv4 配置。

## 任务

- 创建一个名为 `dummy0-static` 的新 NetworkManager 连接配置文件。
- 为 `dummy0` 接口配置该配置文件。
- 分配静态 IPv4 地址 `192.168.100.10/24`。
- 将网关设置为 `192.168.100.1`。
- 确保该连接配置文件设置为在系统启动时自动激活。
- 激活新的连接配置文件。

## 要求

- 必须使用 `nmcli` 命令行工具。
- 新的连接配置文件名称必须准确为 `dummy0-static`。
- 配置必须在重启后依然有效。

## 提示

要使用 nmcli 创建 NetworkManager 连接配置文件，你需要使用 `nmcli connection add` 命令，并配合以下关键参数：

- `type`：指定连接类型（对于 dummy 接口，使用 `dummy`）。
- `con-name`：连接配置文件的名称。
- `ifname`：接口名称。
- `autoconnect`：设置为 `yes` 以实现开机自动激活。
- `ip4`：使用 CIDR 表示法的 IPv4 地址。
- `gw4`：IPv4 网关地址。

基本语法如下：

```bash
sudo nmcli connection add type [TYPE] con-name [NAME] ifname [INTERFACE] autoconnect [yes/no] ip4 [ADDRESS/CIDR] gw4 [GATEWAY]
```

创建连接后，你可能需要使用以下命令激活它：

```bash
sudo nmcli connection up [CONNECTION_NAME]
```

## 示例

成功激活新连接后，`ip addr show dummy0` 的输出应包含该静态 IPv4 地址。

```plaintext
3: dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 12:34:56:78:9a:bc brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.10/24 brd 192.168.100.255 scope global noprefixroute dummy0-static
       valid_lft forever preferred_lft forever
    ...
```

此外，你可以通过 `nmcli con show --active` 验证连接配置文件是否已激活。

```plaintext
NAME           UUID                                  TYPE      DEVICE
dummy0-static  550e8400-e29b-41d4-a716-446655440000  ethernet  dummy0
...
```
