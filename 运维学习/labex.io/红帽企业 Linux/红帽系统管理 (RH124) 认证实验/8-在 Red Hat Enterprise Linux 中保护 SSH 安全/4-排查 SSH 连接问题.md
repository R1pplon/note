# 排查 SSH 连接问题

在这一步中，你将学习如何排查常见的 SSH 连接问题。当 SSH 连接失败时，找出确切的问题可能具有挑战性。`ssh` 命令提供了详细的输出选项，通过显示连接过程的详细信息，可以帮助诊断问题。

`ssh` 命令提供三个详细级别：`-v`、`-vv` 和 `-vvv`。每增加一个 `v`，显示的调试信息量就会增加。

让我们从尝试连接到 localhost 上一个不存在的端口开始，以演示连接失败并查看调试输出。

首先，使用 `-v`（详细）尝试连接到端口 2222（该端口应该没有运行服务）：

```bash
ssh -v -p 2222 labex@localhost
```

你将看到类似以下的输出，表明连接被拒绝：

```plaintext
OpenSSH_8.7p1, OpenSSL 3.0.1 14 Dec 2021
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Reading configuration data /etc/ssh/ssh_config.d/01-training.conf
debug1: /etc/ssh/ssh_config.d/01-training.conf line 1: Applying options for *
debug1: Reading configuration data /etc/ssh/ssh_config.d/50-redhat.conf
debug1: /etc/ssh/ssh_config.d/50-redhat.conf line 3: Applying options for *
debug1: Connecting to localhost [127.0.0.1] port 2222.
ssh: connect to host localhost port 2222: Connection refused
```

现在，让我们使用 `-vv`（更详细）：

```bash
ssh -vv -p 2222 labex@localhost
```

输出将更加详细，提供额外的调试消息：

```plaintext
OpenSSH_8.7p1, OpenSSL 3.0.1 14 Dec 2021
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Reading configuration data /etc/ssh/ssh_config.d/01-training.conf
debug1: /etc/ssh/ssh_config.d/01-training.conf line 1: Applying options for *
debug1: Reading configuration data /etc/ssh/ssh_config.d/50-redhat.conf
debug1: /etc/ssh/ssh_config.d/50-redhat.conf line 3: Applying options for *
debug2: resolving "localhost" port 2222
debug2: ssh_connect_direct: entering
debug1: Connecting to localhost [127.0.0.1] port 2222.
debug1: connect to address 127.0.0.1 port 2222: Connection refused
ssh: connect to host localhost port 2222: Connection refused
```

最后，使用 `-vvv`（最详细）：

```bash
ssh -vvv -p 2222 labex@localhost
```

此级别提供最大量的调试信息，虽然可能让人不知所措，但对于复杂问题非常有用。

```plaintext
OpenSSH_8.7p1, OpenSSL 3.0.1 14 Dec 2021
debug3: ssh_connect_internal: entering
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Reading configuration data /etc/ssh/ssh_config.d/01-training.conf
debug1: /etc/ssh/ssh_config.d/01-training.conf line 1: Applying options for *
debug1: Reading configuration data /etc/ssh/ssh_config.d/50-redhat.conf
debug1: /etc/ssh/ssh_config.d/50-redhat.conf line 3: Applying options for *
debug2: resolving "localhost" port 2222
debug2: ssh_connect_direct: entering
debug1: Connecting to localhost [127.0.0.1] port 2222.
debug1: connect to address 127.0.0.1 port 2222: Connection refused
ssh: connect to host localhost port 2222: Connection refused
```

在这种情况下，错误 `Connection refused` 清楚地表明端口 2222 上没有运行 SSH 服务器。

现在，让我们模拟一个常见问题：主机密钥已更改。在第一步中，你连接到了 `localhost`，其公钥被添加到了你的 `~/.ssh/known_hosts` 文件中。如果 SSH 服务器密钥发生更改（例如，由于服务器重建或恶意攻击），你的 SSH 客户端将检测到此不匹配并拒绝连接。

为了模拟这一点，我们将故意修改 `localhost` 的 `known_hosts` 条目，使其无效。

首先，使用 `nano` 打开 `~/.ssh/known_hosts` 文件：

```bash
nano ~/.ssh/known_hosts
```

你将看到多行具有不同密钥类型的条目：

```plaintext
localhost ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHvl7dcZkvMNOr3cjKjlR2/JgFbGpURThT/bHnLZN6gG
localhost ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCynhy3601o9ZSGZoY0KB/QSonk5ykod2Tb7sCAqVn4ZgTCwd96BhPjJLPNQ6ldNASo1e7EzfT4BUjG5T0ZDRhgaI65qmDwITWipTWUfmYT5XoScyf6NDhcRxYiJwztFEkOvLcPhelS6UXj5Z7HdmYH4Nc5wiF00Wah3Jc0/2CfQsFZCXTn/7Kp8KKbBbPqPzr2R3WIULEacOxx9HKVv+2TvYg/OHZz40hTsr1c68DD7h5PMBNe21YB3HLRRk2LQEC7v7BFD+DCek9GNR66JBjbLDljtwWCaPCY0UntBjjvJ3W2LhX5RDZQHV/iaUSj2tEXnvPt9KSqGfBS91D12dBXyOmWVnTpvvI17BdDkEeefas2Uz4d7Bv/PDxZR6IKkaIGQ/ZnRhSEhBNvfqlBGqkOhRr6jQJK+rQMnsZCT6OEgW7osWzkw5Bs1wY/RNToeQqrRMclqffO9plFI688N2iT86+nxrvBVZg4yMMm2J1lleaBvinXCB8jE6lrtwoAdgk=
localhost ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKYWY8Ty6TrbQS/0fUljBWuUpkyPCS/5P6ZwxhSYsqjRBIprMANI/JQotZqHYq2w3b2X/n8O+J3/WuIB6XMl1f4=
```

选择其中一行进行修改。在本例中，让我们修改 ED25519 密钥（第一行）。修改长密钥字符串中的几个字符（例如，将最后一个字符从 `G` 改为 `A`）。注意不要删除整行或文件的其他部分。

例如，将：
`...ZN6gG`
改为：
`...ZN6gA`

按 `Ctrl+X` 保存文件，然后按 `Y` 确认保存，按 `Enter` 确认文件名。

现在，尝试再次连接到 `localhost`：

```bash
ssh labex@localhost
```

你将收到关于主机密钥已更改的警告：

```plaintext
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:h5k1mmPFylpxUCsKx+Mf8rN4wOrk9TmyRfzTvGWRm7A.
Please contact your system administrator.
Add correct host key in /home/labex/.ssh/known_hosts to get rid of this message.
Offending key in /home/labex/.ssh/known_hosts:1
ED25519 host key for localhost has changed and you have requested strict checking.
Host key verification failed.
```

这是一个严重的安全警告。如果你在现实场景中遇到这种情况，你应该调查主机密钥更改的原因。如果是合法的更改（例如服务器重新安装），你需要从 `known_hosts` 中删除旧条目。

要解决此问题，你可以手动编辑 `~/.ssh/known_hosts` 并删除有问题的行，或者使用 `ssh-keygen -R` 删除 `localhost` 的条目。

让我们使用 `ssh-keygen -R` 删除错误的条目：

```bash
ssh-keygen -R localhost
```

```plaintext
# Host localhost found: line 1
# Host localhost found: line 2
# Host localhost found: line 3
/home/labex/.ssh/known_hosts updated.
Original contents retained as /home/labex/.ssh/known_hosts.old
```

现在，尝试再次连接到 `localhost`。系统会提示你确认主机的真实性，就像你第一次连接时一样。

```bash
ssh labex@localhost
```

```plaintext
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ED25519 key fingerprint is SHA256:h5k1mmPFylpxUCsKx+Mf8rN4wOrk9TmyRfzTvGWRm7A.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'localhost' (ED25519) to the list of known hosts.
Last login: Mon Jun  9 01:40:03 2025 from 127.0.0.1
[labex@host ~]$
```

你现在已成功再次使用基于密钥的身份验证连接。

退出会话：

```bash
exit
```

```plaintext
exit
Connection to localhost closed.
[labex@host ~]$
```
