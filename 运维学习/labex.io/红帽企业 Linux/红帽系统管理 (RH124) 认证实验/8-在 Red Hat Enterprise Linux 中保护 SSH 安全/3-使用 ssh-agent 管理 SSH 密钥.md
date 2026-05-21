# 使用 ssh-agent 管理 SSH 密钥

在这一步中，你将学习如何使用 `ssh-agent` 管理你的 SSH 密钥。`ssh-agent` 是一个在后台运行的程序，它将你的私钥保存在内存中。当你的私钥受到密码短语保护时，这特别有用。你无需在每次使用密钥时都输入密码短语，只需在将密钥添加到 `ssh-agent` 时输入一次，之后代理就会在你的会话期间为你处理身份验证。

尽管你在上一步中生成了一个没有密码短语的密钥，但我们现在将创建一个带有密码短语的新密钥，以演示 `ssh-agent` 的用途。

首先，生成一个带有密码短语的新 SSH 密钥对。我们将此密钥命名为 `id_rsa_passphrase`，以将其与默认的 `id_rsa` 密钥区分开来。

```bash
ssh-keygen -f ~/.ssh/id_rsa_passphrase
```

系统会提示你输入密码短语。在本实验中，请使用 `mypassphrase` 作为密码短语。

```plaintext
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): mypassphrase
Enter same passphrase again: mypassphrase
Your identification has been saved in /home/labex/.ssh/id_rsa_passphrase
Your public key has been saved in /home/labex/.ssh/id_rsa_passphrase.pub
The key fingerprint is:
SHA256:BuSxVlJb1lsiUFi2I5DAvyL01fJ5d480LT86dgtcHEg labex@6846375f1c0e35fea6cb03e6
The key's randomart image is:
+---[RSA 3072]----+
|   ...=o+=*. E   |
|    .o.*.=..+ o  |
|     .=.o o. = . |
|  .  .+... .. . .|
| . . . +S.     + |
|  . o ..o . o * .|
|   . .   . . = * |
|             oooo|
|            ..+.o|
+----[SHA256]-----+
```

**注意：** 如果你不小心在没有输入密码短语的情况下按了 Enter，密钥将在没有密码短语的情况下创建。在这种情况下，你可以删除这些文件并再次运行该命令，确保在提示时输入 `mypassphrase`。

现在，让我们将这个新的公钥复制到 `localhost`，以便你可以使用它进行身份验证。

```bash
ssh-copy-id -i ~/.ssh/id_rsa_passphrase.pub labex@localhost
```

由于你已经使用默认密钥设置了免密登录，该命令可能不会提示输入密码，而是使用你现有的身份验证：

```plaintext
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/labex/.ssh/id_rsa_passphrase.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'labex@localhost'"
and check to make sure that only the key(s) you wanted were added.
```

现在，尝试使用此新密钥连接到 `localhost`。你需要使用 `-i` 选项指定私钥文件。

```bash
ssh -i ~/.ssh/id_rsa_passphrase labex@localhost
```

如果你为密钥设置了密码短语，系统会提示你输入它。但是，如果你不小心在没有密码短语的情况下创建了密钥（如示例输出所示），你将直接登录：

```plaintext
Last login: Mon Jun  9 01:39:25 2025 from 47.251.66.143
[labex@host ~]$
```

你已登录。现在，退出会话：

```bash
exit
```

```plaintext
exit
Connection to localhost closed.
[labex@host ~]$
```

**注意：** 如果你的密钥没有密码短语，你仍然可以继续进行 `ssh-agent` 演示以了解其工作原理，尽管在这种情况下它不会提示输入密码短语。

首先，在当前的 shell 会话中启动 `ssh-agent`。`eval` 命令用于正确设置 `ssh-agent` 输出的环境变量。

```bash
eval "$(ssh-agent)"
```

```plaintext
Agent pid 1024
```

输出将显示 `ssh-agent` 的进程 ID (PID)。

接下来，将你的私钥 (`id_rsa_passphrase`) 添加到 `ssh-agent`。

```bash
ssh-add ~/.ssh/id_rsa_passphrase
```

如果你的密钥有密码短语，系统会提示你输入它。如果没有，密钥将直接添加：

```plaintext
Identity added: /home/labex/.ssh/id_rsa_passphrase (labex@6846375f1c0e35fea6cb03e6)
```

现在密钥已添加到 `ssh-agent`，尝试再次使用相同的密钥连接到 `localhost`。

```bash
ssh -i ~/.ssh/id_rsa_passphrase labex@localhost
```

你应该能够连接而无需输入密码短语（无论你的密钥是否有密码短语，因为它现在由代理管理）：

```plaintext
Last login: Mon Jun  9 01:39:49 2025 from 127.0.0.1
[labex@host ~]$
```

你已成功使用 `ssh-agent` 管理你的 SSH 密钥。

**重要提示：** `ssh-agent` 环境变量仅在启动它的 shell 会话中可用。如果你在 SSH 会话中，则需要退出回到本地 shell 才能使用 `ssh-add` 命令。

先退出 SSH 会话：

```bash
exit
```

```plaintext
exit
Connection to localhost closed.
[labex@host ~]$
```

现在，要查看当前加载在 `ssh-agent` 中的密钥，可以使用 `ssh-add -l`：

```bash
ssh-add -l
```

如果代理正在运行且已加载密钥，你将看到如下输出：

```plaintext
3072 SHA256:BuSxVlJb1lsiUFi2I5DAvyL01fJ5d480LT86dgtcHEg /home/labex/.ssh/id_rsa_passphrase (RSA)
```

但是，如果你看到类似“Could not open a connection to your authentication agent”的错误消息，则意味着代理环境变量未在当前会话中设置。

要从 `ssh-agent` 中删除所有身份，请使用 `ssh-add -D`：

```bash
ssh-add -D
```

如果代理可访问，你将看到：

```plaintext
All identities removed.
```

但是，如果你看到“Could not open a connection to your authentication agent”，则意味着代理环境在当前会话中不可用。

现在，如果你尝试再次连接且你的密钥有密码短语，系统会提示你输入它，因为密钥已从代理中删除：

```bash
ssh -i ~/.ssh/id_rsa_passphrase labex@localhost
```

如果你的密钥有密码短语，你将看到：

```plaintext
Enter passphrase for key '/home/labex/.ssh/id_rsa_passphrase':
```

如果你的密钥没有密码短语，你仍然可以直接连接。如果提示输入密码短语，请按 `Ctrl+C` 取消连接尝试。

最后，要停止 `ssh-agent` 进程，可以使用 `ssh-agent -k`：

```bash
ssh-agent -k
```

如果未设置 SSH_AGENT_PID 环境变量，你可能会看到：

```plaintext
SSH_AGENT_PID not set, cannot kill agent
```

如果代理是在不同的 shell 会话中启动的，或者环境变量未正确导出，这是正常现象。
