# 生成并使用 SSH 密钥对实现免密登录

在这一步中，你将学习如何生成 SSH 密钥对并将其用于免密登录。基于 SSH 密钥的身份验证是密码验证的一种更安全、更便捷的替代方案。你无需每次连接时都输入密码，而是使用一对加密密钥：私钥（保存在本地机器上，必须保密）和公钥（放置在远程服务器上）。

首先，你需要生成一个 SSH 密钥对。为此，你将使用 `ssh-keygen` 命令。默认情况下，`ssh-keygen` 会创建一个 RSA 密钥对，并将私钥保存在 `~/.ssh/id_rsa` 中，将公钥保存在 `~/.ssh/id_rsa.pub` 中。

执行 `ssh-keygen` 命令：

```bash
ssh-keygen
```

系统会提示你进行一些选择：

```plaintext
Generating public/private rsa key pair.
Enter file in which to save the key (/home/labex/.ssh/id_rsa):
```

按 `Enter` 接受默认文件路径（`/home/labex/.ssh/id_rsa`）。

```plaintext
Enter passphrase (empty for no passphrase):
```

在本实验中，按两次 `Enter` 将密码短语留空。虽然在实际场景中建议使用密码短语以增加一层安全保障，但为了简单起见并直接演示免密登录，我们在此跳过它。

```plaintext
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/labex/.ssh/id_rsa
Your public key has been saved in /home/labex/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:QoV7pNBFu1kafGP3VJhpZuIdr1zc+qamJ1C2YAadgNY labex@6846375f1c0e35fea6cb03e6
The key's randomart image is:
+---[RSA 3072]----+
|     . *=o .   +.|
|    . =oE.o . O. |
|     o.++.=..*.+.|
|     .o .O+o+o. =|
|      ..So + o.+ |
|       .  . . +  |
|           .   . |
|            . o o|
|            .=.o |
+----[SHA256]-----+
```

现在，验证密钥文件是否已在 `~/.ssh/` 目录中创建：

```bash
ls -l ~/.ssh/
```

```plaintext
total 16
-rw------- 1 labex labex 2622 Jun  9 01:37 id_rsa
-rw-r--r-- 1 labex labex  584 Jun  9 01:37 id_rsa.pub
-rw------- 1 labex labex  825 Jun  9 01:35 known_hosts
-rw-r--r-- 1 labex labex   91 Jun  9 01:35 known_hosts.old
```

你应该能看到 `id_rsa`（你的私钥）和 `id_rsa.pub`（你的公钥）。请注意权限：`id_rsa` 的权限为 `rw-------`（仅所有者可读写），这对安全性至关重要。你可能还会看到 `known_hosts.old`，它是之前 `known_hosts` 文件的备份。

接下来，你需要复制你的公钥以启用免密登录。`ssh-copy-id` 命令就是为此设计的。它会将你的公钥追加到 `~/.ssh/authorized_keys` 文件中，从而允许你无需密码即可登录。

执行 `ssh-copy-id` 命令，指定用户和主机名：

```bash
ssh-copy-id labex@localhost
```

系统会提示你输入 `labex` 用户的密码。输入 `labex` 并按 `Enter`。

```plaintext
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/labex/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
labex@localhost's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'labex@localhost'"
and check to make sure that only the key(s) you wanted were added.
```

命令输出确认已添加了一个密钥。现在，尝试以 `labex` 身份登录 `localhost`，无需提供密码：

```bash
ssh labex@localhost
```

如果配置正确，你应该无需输入密码即可通过 SSH 登录。

```plaintext
Last login: Mon Jun  9 01:37:39 2025 from 47.251.66.143
[labex@host ~]$
```

你已成功配置了使用密钥对的 SSH 免密登录！

要退出远程会话，请输入 `exit`：

```bash
exit
```

```plaintext
exit
Connection to localhost closed.
[labex@host ~]$
```
