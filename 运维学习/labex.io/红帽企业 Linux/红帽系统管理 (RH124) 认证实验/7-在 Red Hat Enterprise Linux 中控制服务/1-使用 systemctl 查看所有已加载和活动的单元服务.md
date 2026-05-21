# 使用 systemctl 查看所有已加载和活动的单元服务

在此步骤中，你将学习如何使用 `systemctl` 命令识别自动启动的系统进程。`systemctl` 是在 Red Hat Enterprise Linux 中管理 `systemd` 服务的首选工具。

首先，让我们探索如何列出所有当前已加载和激活的服务单元。`systemctl list-units --type=service` 命令用于此目的。此命令显示 `systemd` 守护进程已成功解析并加载到内存中，并且当前正在运行的服务单元。

在 RHEL 环境中打开你的终端。你已作为 `labex` 用户登录，当前目录是 `~/project`。

执行以下命令列出所有已加载和激活的服务单元：

```bash
systemctl list-units --type=service
```

你将看到类似以下的输出，显示各种服务及其状态：

```plaintext
UNIT                  LOAD    ACTIVE  SUB      DESCRIPTION
atd.service           loaded  active  running  Job spooling tools
auditd.service        loaded  active  running  Security Auditing Service
chronyd.service       loaded  active  running  NTP client/server
crond.service         loaded  active  running  Command Scheduler
dbus-broker.service   loaded  active  running  D-Bus System Message Bus
...output omitted...
```

让我们分解一下输出中的列：

- **UNIT**: 这是服务单元的名称，通常以 `.service` 结尾。
- **LOAD**: 指示 `systemd` 守护进程是否成功解析了单元的配置并将其加载到内存中。`loaded` 表示成功。
- **ACTIVE**: 这是单元的高级激活状态。`active` 通常表示单元已成功启动。
- **SUB**: 这是低级激活状态，提供更详细的信息。对于正在运行的服务，`running` 是常见的状态。
- **DESCRIPTION**: 对服务功能的简要描述。

按 `q` 退出命令。

接下来，你可以将 `--all` 选项与 `systemctl list-units --type=service` 一起使用，以列出所有服务单元，无论其激活状态如何（active、inactive、failed 等）。这对于查看已安装但当前未运行的服务很有用。

执行以下命令：

```bash
systemctl list-units --type=service --all
```

输出将包括处于 `inactive` 或其他状态的服务，提供更全面的视图：

```plaintext
UNIT                          LOAD      ACTIVE   SUB     DESCRIPTION
  atd.service                 loaded    active   running Job spooling tools
  auditd.service              loaded    active   running Security Auditing ...
  auth-rpcgss-module.service  loaded    inactive dead    Kernel Module ...
  chronyd.service             loaded    active   running NTP client/server
  cpupower.service            loaded    inactive dead    Configure CPU power ...
  crond.service               loaded    active   running Command Scheduler
  dbus-broker.service         loaded    active   running  D-Bus System Message Bus
● display-manager.service     not-found inactive dead    display-manager.service
...output omitted...
```

最后，要查看 *所有* 已安装单元文件（包括未加载或未激活的）的状态，你可以使用 `systemctl list-unit-files --type=service`。此命令显示服务是 `enabled`（启动时启动）、`disabled`（启动时不启动）、`static`（不能直接启用但可能被其他单元启动）还是 `masked`（阻止启动）。

执行以下命令：

```bash
systemctl list-unit-files --type=service
```

你将看到类似以下的输出，指示每个服务单元文件的 `STATE` 和 `VENDOR PRESET`：

```plaintext
UNIT FILE                         STATE       VENDOR PRESET
arp-ethers.service                disabled    disabled
atd.service                       enabled     enabled
auditd.service                    enabled     enabled
auth-rpcgss-module.service        static      -
autovt@.service                   alias       -
blk-availability.service          disabled    disabled
...output omitted...
```

此命令特别有助于了解哪些服务配置为在系统启动时自动启动。
