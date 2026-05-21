# 使用 timedatectl 和 chronyd 维护精确的系统时间

在这一步，你将学习如何使用 `timedatectl` 命令维护准确的系统时间，并了解 `chronyd` 服务的作用。准确的时间对于日志记录、安全性和许多网络服务至关重要。

**1. 使用 `timedatectl` 管理系统时间和时区：**

`timedatectl` 命令提供了当前时间相关系统设置的概述，包括本地时间、通用时间（UTC）、RTC 时间、时区和 NTP 同步状态。

让我们检查你系统的当前时间设置：

```bash
timedatectl
```

你应该看到类似于这样的输出（确切的时间和日期将反映你当前的系统时间）：

```plaintext
               Local time: Sun 2025-06-15 21:46:11 EDT
           Universal time: Mon 2025-06-16 01:46:11 UTC
                 RTC time: Mon 2025-06-16 01:46:10
                Time zone: America/New_York (EDT, -0400)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

你可以使用 `list-timezones` 选项列出所有可用的时区：

```bash
timedatectl list-timezones | less
```

按 `q` 退出 `less`。时区是根据互联网号码分配机构（IANA）时区数据库命名的，通常按洲/大洋，然后是最大的城市。

要更改系统的时区，你使用 `set-timezone` 选项。例如，让我们将时区更改为 `America/Phoenix`。你需要 `sudo` 权限才能执行此操作。

```bash
sudo timedatectl set-timezone America/Phoenix
```

现在，验证更改：

```bash
timedatectl
```

你应该看到时区已更新为 `America/Phoenix`。

你还可以使用 `set-time` 选项手动设置系统当前时间。格式为 "YYYY-MM-DD hh:mm:ss"，但你可以省略日期或时间。让我们将时间设置为 `09:00:00`（对于当前日期）。

```bash
sudo timedatectl set-time 09:00:00
```

验证时间更改：

```bash
timedatectl
```

最后，`set-ntp` 选项启用或禁用 NTP 同步以进行自动时间调整。它将 `true` 或 `false` 作为参数。让我们暂时禁用 NTP 同步（稍后我们将重新启用它）。

```bash
sudo timedatectl set-ntp false
```

验证 NTP 服务状态：

```bash
timedatectl
```

你应该看到 `NTP service: inactive`。

**2. 了解和配置 `chronyd` 服务：**

`chronyd` 服务是一个守护进程，它通过与网络时间协议（NTP）服务器同步来保持系统实时时钟（RTC）的准确性。它是 Red Hat Enterprise Linux 中的默认 NTP 客户端。

`chronyd` 的配置文件是 `/etc/chrony.conf`。默认情况下，它使用公共 NTP 服务器。在实际场景中，你可能将其配置为使用内部 NTP 服务器。

让我们查看默认的 `chrony.conf` 文件。

```bash
cat /etc/chrony.conf
```

你将看到以 `server` 或 `pool` 开头的行，这些行定义了 NTP 源。建议使用 `iburst` 选项，因为它会快速进行四次测量以获得更准确的初始同步。

NTP 时间源的 `stratum` 表示其质量。`stratum 0` 是一个参考时钟，`stratum 1` 直接连接到参考时钟，而 `stratum 2` 从 `stratum 1` 服务器同步。

由于在此容器环境中不可使用 `systemctl`，因此我们无法直接重启 `chronyd` 以应用配置更改。但是，我们可以通过修改文件来模拟配置更改。

让我们使用 `timedatectl` 重新启用 NTP 同步。

```bash
sudo timedatectl set-ntp true
```

再次验证 NTP 服务状态：

```bash
timedatectl
```

你应该看到 `NTP service: active`。

`chronyc` 命令充当 `chronyd` 服务的客户端。你可以使用它来监视同步状态。`chronyc sources` 命令显示当前的时间源及其同步状态。

```bash
chronyc sources -v
```

输出将显示有关 NTP 源的详细信息。`S`（源状态）字段中的星号 `*` 表示 `chronyd` 当前同步到的源。

```plaintext
  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* 100.100.61.88                 1   5   377    16  +1824us[+2180us] +/-   85ms
...output omitted...
```

此输出确认你的系统正在主动将其时间与 NTP 服务器同步。
