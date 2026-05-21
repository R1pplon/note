# 自定义 SSH 客户端配置

在这一步中，你将学习如何使用 `~/.ssh/config` 文件自定义你的 SSH 客户端配置。此文件允许你为不同的远程主机定义别名和特定的连接参数，从而简化你的 SSH 命令并使其更加一致。

`~/.ssh/config` 文件是管理 SSH 连接的强大工具。你可以指定各种选项，例如用户名、要使用的私钥文件、端口号，甚至更高级的设置（如代理命令）。

首先，让我们创建或打开 `~/.ssh/config` 文件。如果它不存在，`nano` 将创建它。

```bash
nano ~/.ssh/config
```

将以下配置添加到文件中。此配置定义了一个别名 `localhost_labex`，用于以 `labex` 用户身份连接到 `localhost`；以及一个别名 `localhost_root`，用于以 `root` 用户身份连接。它还明确指定了 `labex` 用户的 `IdentityFile`，以使用在之前步骤中生成的 `id_rsa` 密钥。

```config
Host localhost_labex
    HostName localhost
    User labex
    IdentityFile ~/.ssh/id_rsa

Host localhost_root
    HostName localhost
    User root
```

按 `Ctrl+X` 保存文件，然后按 `Y` 确认保存，按 `Enter` 确认文件名。

现在，让我们尝试使用这些新别名连接到 `localhost`。

使用 `localhost_labex` 别名以 `labex` 身份连接：

```bash
ssh localhost_labex
```

由于你配置了 `IdentityFile ~/.ssh/id_rsa` 且 `id_rsa` 没有密码短语，你应该无需输入密码即可登录。

```plaintext
Last login: Mon Jun  9 01:54:16 2025 from 47.251.66.143
[labex@host ~]$
```

退出会话：

```bash
exit
```

```plaintext
exit
Connection to localhost closed.
[labex@host ~]$
```

现在，使用 `localhost_root` 别名以 `root` 身份连接：

```bash
ssh localhost_root
```

系统会提示你输入 `root` 用户的密码。但是，由于在此环境中禁用了 root 登录，你将收到“Permission denied”消息：

```plaintext
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
```

按 `Ctrl+C` 取消连接尝试：

```plaintext
^C
```

这表明 SSH 配置别名有效，但由于禁用 root 登录的安全策略，连接失败。

如你所见，使用 `~/.ssh/config` 文件通过预配置常见的连接参数简化了你的 SSH 命令。

让我们添加另一个条目来演示指定不同的端口。虽然 `localhost` 使用默认的 SSH 端口 (22)，但此示例展示了如果端口不同，你将如何配置它。

再次打开 `~/.ssh/config` 文件：

```bash
nano ~/.ssh/config
```

添加以下条目。这创建了一个别名 `localhost_port_example`，它明确将端口设置为 `2222`。（注意：`localhost` 实际上并没有在端口 2222 上监听，因此此连接将失败，但这演示了配置方法。）

```config
Host localhost_labex
    HostName localhost
    User labex
    IdentityFile ~/.ssh/id_rsa

Host localhost_root
    HostName localhost
    User root

Host localhost_port_example
    HostName localhost
    Port 2222
    User labex
```

保存文件。

现在，尝试使用 `localhost_port_example` 别名连接：

```bash
ssh localhost_port_example
```

此连接将失败，因为 `localhost` 没有在端口 2222 上监听，但这演示了如何在 SSH 配置中指定自定义端口。

```plaintext
ssh: connect to host localhost port 2222: Cannot assign requested address

You can find some explanations for typical errors at this link:
            https://red.ht/support_rhel_ssh
```

你可以查看当前的 SSH 配置以查看所有定义的别名：

```bash
cat ~/.ssh/config
```

```plaintext
Host localhost_labex
    HostName localhost
    User labex
    IdentityFile ~/.ssh/id_rsa

Host localhost_root
    HostName localhost
    User root

Host localhost_port_example
    HostName localhost
    Port 2222
    User labex
```

最后，让我们通过删除 `localhost_port_example` 条目来清理 `~/.ssh/config` 文件。

打开 `~/.ssh/config` 文件：

```bash
nano ~/.ssh/config
```

删除 `Host localhost_port_example` 块。文件应如下所示：

```config
Host localhost_labex
    HostName localhost
    User labex
    IdentityFile ~/.ssh/id_rsa

Host localhost_root
    HostName localhost
    User root
```

保存文件。
