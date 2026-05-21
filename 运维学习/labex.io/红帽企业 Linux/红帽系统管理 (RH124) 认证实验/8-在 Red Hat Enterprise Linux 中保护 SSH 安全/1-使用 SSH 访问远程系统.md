# 使用 SSH 访问远程系统

在这一步中，你将学习如何使用 SSH（Secure Shell）访问远程系统。SSH 是一种加密网络协议，用于在不安全的网络上安全地操作网络服务。它通过客户端-服务器架构，连接 SSH 客户端与 SSH 服务器，从而在不安全的网络上提供安全通道。

**注意：** 在本实验环境中，出于安全考虑，可能已经配置了一些安全功能（如 root 登录限制）。这是正常现象，体现了最佳实践的应用。

在连接到远程系统之前，请通过安装 OpenSSH 客户端和服务器软件包并启动 SSH 服务来准备实验环境。SSH 使用客户端-服务器架构：客户端（`ssh`）发起连接，服务器（`sshd`）接受连接。你还将安装 `nano`，稍后将使用它来编辑 SSH 配置文件。

运行以下命令安装所需的软件包。`-y` 标志会自动确认软件包安装提示：

```bash
sudo dnf install -y openssh-clients openssh-server nano
```

启动 SSH 服务并将其配置为在开机时自动启动：

```bash
sudo systemctl start sshd
sudo systemctl enable sshd
```

验证 SSH 服务是否正在运行：

```bash
sudo systemctl status sshd
```

你应该在输出中看到 `Active: active (running)`。按 `q` 退出状态视图。

首先，你将使用 SSH 连接到本地系统。即使连接到同一台机器，这也演示了 SSH 的客户端-服务器架构。你将使用 `ssh` 命令连接到 `localhost`。

`ssh` 的基本语法为：
`ssh [username]@[hostname_or_IP]`

如果你不指定用户名，SSH 将尝试使用你当前的本地用户名登录。在本例中，你的本地用户名是 `labex`。

让我们尝试使用当前用户名连接到 `localhost`：

```bash
ssh localhost
```

当你第一次连接时，SSH 会要求你确认主机的真实性。这是一项防止中间人攻击的安全措施。输入 `yes` 并按 `Enter`。

```plaintext
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ED25519 key fingerprint is SHA256:h5k1mmPFylpxUCsKx+Mf8rN4wOrk9TmyRfzTvGWRm7A.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'localhost' (ED25519) to the list of known hosts.
labex@localhost's password:
```

系统会提示你输入 `labex` 用户的密码。输入 `labex` 并按 `Enter`。

```plaintext
labex@localhost's password:
Last login: Mon Jun  9 01:34:39 2025 from 47.251.66.143
[labex@host ~]$
```

你现在已通过 SSH 登录到 localhost。请注意，你的提示符可能显示为 `[labex@localhost ~]$`，表明你已通过 SSH 连接。

要注销 SSH 会话，请使用 `exit` 命令：

```bash
exit
```

```plaintext
logout
Connection to localhost closed.
[labex@host ~]$
```

现在，让我们尝试以 `root` 用户身份连接到 `localhost`。请注意，在此环境中，出于安全原因，root 登录可能默认被禁用。

```bash
ssh root@localhost
```

系统会提示你输入 root 密码。但是，如果 root 登录被禁用，你可能会遇到“Permission denied”（权限被拒绝）的消息：

```plaintext
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
```

如果 root 登录被禁用，这是预期的行为，也体现了安全最佳实践。你可以按 `Ctrl+C` 取消连接尝试。

SSH 也可用于在远程系统上执行单个命令，而无需打开交互式 shell。这对于快速检查或自动化非常有用。

让我们以 `labex` 用户身份在 `localhost` 上运行 `hostname` 命令：

```bash
ssh labex@localhost hostname
```

系统会提示你输入 `labex` 用户的密码。输入 `labex` 并按 `Enter`。

```plaintext
labex@localhost's password:
6846375f1c0e35fea6cb03e6
[labex@host ~]$
```

`hostname` 命令已通过 SSH 执行，其输出显示在你的本地终端上。你并没有进入交互式 shell。

最后，让我们探讨 SSH 如何管理已知主机。当你连接到新的 SSH 服务器时，其公钥指纹会被添加到你的 `~/.ssh/known_hosts` 文件中。此文件有助于你的 SSH 客户端在未来的连接中验证服务器的身份。

你可以查看 `~/.ssh/known_hosts` 文件的内容：

```bash
cat ~/.ssh/known_hosts
```

```plaintext
localhost ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHvl7dcZkvMNOr3cjKjlR2/JgFbGpURThT/bHnLZN6gG
localhost ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCynhy3601o9ZSGZoY0KB/QSonk5ykod2Tb7sCAqVn4ZgTCwd96BhPjJLPNQ6ldNASo1e7EzfT4BUjG5T0ZDRhgaI65qmDwITWipTWUfmYT5XoScyf6NDhcRxYiJwztFEkOvLcPhelS6UXj5Z7HdmYH4Nc5wiF00Wah3Jc0/2CfQsFZCXTn/7Kp8KKbBbPqPzr2R3WIULEacOxx9HKVv+2TvYg/OHZz40hTsr1c68DD7h5PMBNe21YB3HLRRk2LQEC7v7BFD+DCek9GNR66JBjbLDljtwWCaPCY0UntBjjvJ3W2LhX5RDZQHV/iaUSj2tEXnvPt9KSqGfBS91D12dBXyOmWVnTpvvI17BdDkEeefas2Uz4d7Bv/PDxZR6IKkaIGQ/ZnRhSEhBNvfqlBGqkOhRr6jQJK+rQMnsZCT6OEgW7osWzkw5Bs1wY/RNToeQqrRMclqffO9plFI688N2iT86+nxrvBVZg4yMMm2J1lleaBvinXCB8jE6lrtwoAdgk=
localhost ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKYWY8Ty6TrbQS/0fUljBWuUpkyPCS/5P6ZwxhSYsqjRBIprMANI/JQotZqHYq2w3b2X/n8O+J3/WuIB6XMl1f4=
```

这些条目确认你的客户端已记录了 `localhost` 的多个公钥（ED25519、RSA 和 ECDSA）。SSH 服务器可能支持多种密钥类型以实现兼容性。如果这些服务器密钥中的任何一个发生意外更改，SSH 将向你发出警告，这是一项至关重要的安全功能。
