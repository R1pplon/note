# 添加具有静态 IP 的新网络连接

在这一步中，你将学习如何使用 `nmcli` 命令行工具添加具有静态 IP 地址的新网络连接。`nmcli` 是一个强大的实用程序，用于控制 NetworkManager，它管理 Red Hat Enterprise Linux 上的网络连接。

首先，让我们检查当前的网络设备状态，以识别可用的接口。这将帮助我们选择要配置的接口。

```bash
nmcli dev status
```

你将看到类似这样的输出，显示诸如 `eth0` 和 `eth1` 之类的设备及其各自的连接名称：

```plaintext
DEVICE  TYPE      STATE                   CONNECTION
eth0    ethernet  connected               System eth0
eth1    ethernet  connected               System eth1
lo      loopback  connected (externally)  lo
```

对于这个实验（Lab），我们将使用 `eth0` 接口来创建新的静态连接。请注意，你的系统已经具有名为 `System eth0` 和 `System eth1` 的活动连接，这些连接由 NetworkManager 自动生成。

现在，让我们向 `eth0` 接口添加一个名为 `static-eth0` 的新网络连接。我们将使用静态 IPv4 地址、子网掩码和网关来配置它。基于当前的网络环境（172.16.50.0/24），我们将使用以下详细信息：

- **连接名称：** `static-eth0`
- **接口名称：** `eth0`
- **IPv4 地址：** `172.16.50.200/24`（这意味着 IP 地址 172.16.50.200，带有 24 位子网掩码）
- **网关：** `172.16.50.253`（与当前网关相同）

执行以下命令以添加新连接。请记住使用 `sudo`，因为网络配置更改需要 root 权限。系统不会提示你输入密码。

**注意：** 如果你已经使用不同的 IP 范围（例如 192.168.1.10/24）创建了 `static-eth0` 连接，你应该首先删除它，然后使用此环境的正确 IP 范围重新创建它：

```bash
# 如果存在具有错误 IP 范围的现有连接，则将其删除
sudo nmcli con delete static-eth0
```

```bash
# 使用正确的 IP 范围添加新连接
sudo nmcli con add con-name static-eth0 type ethernet ifname eth0 ipv4.addresses 172.16.50.200/24 ipv4.gateway 172.16.50.253 ipv4.method manual
```

执行该命令后，你应该会看到一条确认消息，表明连接已成功添加：

```plaintext
Connection 'static-eth0' (d4c42169-4134-4d3a-9b31-e837d62601bd) successfully added.
```

让我们分解一下该命令：

- `sudo nmcli con add`：这是添加新的 NetworkManager 连接的基本命令。
- `con-name static-eth0`：这会将名称 `static-eth0` 分配给我们的新连接配置文件。
- `type ethernet`：指定这是一个以太网类型的连接。
- `ifname eth0`：将此连接配置文件绑定到 `eth0` 网络接口。
- `ipv4.addresses 172.16.50.200/24`：设置静态 IPv4 地址和子网掩码。
- `ipv4.gateway 172.16.50.253`：设置此连接的默认网关。
- `ipv4.method manual`：将 IPv4 地址分配方法配置为手动（静态），防止它尝试通过 DHCP 获取 IP 地址。

现在，让我们验证是否已创建新的连接配置文件。我们可以使用 `nmcli con show` 列出所有可用的连接。

```bash
nmcli con show
```

你应该在你的连接中看到 `static-eth0`。请注意，它尚未激活（未分配设备），而系统生成的连接已激活：

```plaintext
NAME         UUID                                  TYPE      DEVICE
System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
System eth1  9c92fad9-6ecb-3e6c-eb4d-8a47c6f50c04  ethernet  eth1
lo           9eac3150-dd39-47e6-a375-f7165442a8eb  loopback  lo
static-eth0  d4c42169-4134-4d3a-9b31-e837d62601bd  ethernet  --
```

在下一步中，我们将学习如何激活这个新创建的连接。
