# 授权公钥并配置 SSH 服务器

现在你已经拥有了密钥对，接下来必须配置服务器。这涉及两个主要操作：将你的公钥添加到授权密钥列表中，并确保 SSH 服务已配置为接受密钥认证。

## 前提条件

确保你当前仍以 `testuser` 账户登录。如果不是，请切换过去：

```bash
su - testuser
# Password: testuser123
```

## 任务

- 将你的公钥复制到 `~/.ssh/authorized_keys` 文件中。
- 为 `~/.ssh/authorized_keys` 设置正确的文件权限。
- 修改 SSH 服务器配置文件（`/etc/ssh/sshd_config`）以启用公钥认证。
- 重新加载 SSH 服务以应用配置更改。

## 要求

- 必须创建 `~/.ssh/authorized_keys` 文件，且该文件需包含公钥（`~/.ssh/id_rsa.pub`）的内容。
- `~/.ssh/authorized_keys` 的权限必须设置为 `600`。
- 在 `/etc/ssh/sshd_config` 中，必须取消 `PubkeyAuthentication` 的注释并将其设置为 `yes`。
- 必须使用 `sudo pkill -HUP sshd` 重新加载 SSH 服务配置。

## 提示

- 你可以使用带有输出重定向（`>>`）的 `cat` 命令将密钥添加到 `authorized_keys` 文件中。
- 使用 `chmod` 命令设置文件权限。
- `testuser` 拥有 `sudo` 权限，可以编辑 `/etc/ssh/sshd_config` 并重新加载 `sshd` 服务。
- 由于在此容器环境中无法使用 `systemctl`，请使用 `pkill` 向 `sshd` 进程发送 `HUP` 信号，这将促使它重新加载配置。

## 示例

完成这些步骤后，你应该能够使用 SSH 密钥登录到 `localhost`，而不会收到输入密码的提示。

```bash
$ ssh testuser@localhost "echo Login successful"
Login successful
```
