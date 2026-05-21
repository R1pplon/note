# 理解 OpenSSH 服务器安全配置

在这一步中，你将通过检查当前的安全配置来了解 OpenSSH 服务器的安全最佳实践。你将了解 root 登录限制和密码验证设置如何协同工作，以保护你的服务器免受未经授权的访问。

**注意：** 在本实验环境中，你将检查当前的 SSH 安全配置并了解不同的安全设置。这一步侧重于理解这些安全措施并学习 SSH 服务器配置的最佳实践。

## 理解 root 登录安全

通常不建议允许通过 SSH 直接进行 root 登录，因为 root 账户拥有完全的管理员权限。如果攻击者获得了 root 账户的访问权限，他们就拥有了对你系统的完全控制权。以普通用户身份登录，然后使用 `sudo` 执行管理任务会更安全。

让我们检查当前的 root 登录配置并测试其有效性。

首先，以 `labex` 用户身份通过 SSH 登录。

```bash
ssh labex@localhost
```

如果之前步骤中的 SSH 密钥设置正确，你应该无需密码即可登录。

```plaintext
Last login: Mon Jun  9 01:57:27 2025 from 47.251.66.143
[labex@host ~]$
```

现在，让我们检查 SSH 服务器配置文件以了解当前的安全设置：

```bash
sudo cat /etc/ssh/sshd_config | grep -E "^PermitRootLogin|^#PermitRootLogin"
```

此命令将显示当前的 PermitRootLogin 设置。你应该看到类似以下内容：

```plaintext
PermitRootLogin no
```

此设置意味着通过 SSH 直接进行 root 登录已被禁用，这是一种安全最佳实践。

让我们测试 root 登录是否确实被阻止。首先，退出当前的 SSH 会话：

```bash
exit
```

```plaintext
exit
Connection to localhost closed.
[labex@host ~]$
```

现在，尝试以 `root` 身份登录 `localhost`：

```bash
ssh root@localhost
```

你应该会看到“Permission denied”消息，表明不允许直接进行 root 登录（这可能已经在你的环境中配置好了）。

```plaintext
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
```

这确认了在此环境中 root 登录已被禁用，这是理想的安全配置。“Permission denied”消息证明了该安全措施正在有效工作。

## 理解密码验证安全

禁用密码验证会强制用户依赖更安全的方法，例如基于 SSH 密钥的身份验证。这显著降低了针对你的服务器进行暴力破解攻击的风险。

让我们检查当前的密码验证设置并了解其工作原理。

以 `labex` 用户身份通过 SSH 登录（使用你的 SSH 密钥）：

```bash
ssh labex@localhost
```

```plaintext
Last login: Mon Jun  9 01:57:32 2025 from 127.0.0.1
[labex@host ~]$
```

首先，让我们检查当前的 PasswordAuthentication 设置：

```bash
sudo cat /etc/ssh/sshd_config | grep PasswordAuthentication
```

```plaintext
PasswordAuthentication yes
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication, then enable this but set PasswordAuthentication
```

如你所见，当前配置为 `PasswordAuthentication yes`，这意味着密码验证已启用。虽然这允许用户使用密码进行身份验证，但也带来了安全风险，因为它使服务器容易受到暴力破解密码攻击。

输出显示：

- `PasswordAuthentication yes` - 这是启用密码验证的活动设置
- 注释行提供了关于 PAM 配置的额外上下文

此配置意味着用户可以使用密码和 SSH 密钥进行身份验证。为了增强安全性，建议禁用密码验证，仅依赖基于 SSH 密钥的身份验证。

退出当前的 SSH 会话：

```bash
exit
```

```plaintext
exit
Connection to localhost closed.
[labex@host ~]$
```

现在，让我们验证在禁用密码验证的同时，基于 SSH 密钥的身份验证是否正常工作：

```bash
ssh labex@localhost
```

这应该无需密码提示即可成功，并使用你的 SSH 密钥：

```plaintext
Last login: Mon Jun  9 02:00:22 2025 from 127.0.0.1
[labex@host ~]$
```

这演示了几个重要的安全概念：

1. **密码验证已启用** (`PasswordAuthentication yes`) - 用户可以使用密码和 SSH 密钥登录
2. **基于 SSH 密钥的身份验证正常工作** - 更安全的身份验证方法可用
3. **服务器允许两种身份验证方法** - 虽然方便，但这可能会带来暴力破解攻击的安全风险
4. **root 登录已禁用** - 管理访问需要适当的用户权限提升

**安全建议：** 对于生产环境，请考虑设置 `PasswordAuthentication no`，通过强制用户仅使用基于 SSH 密钥的身份验证来增强安全性。

退出会话：

```bash
exit
```

```plaintext
exit
Connection to localhost closed.
[labex@host ~]$
```

你已成功检查并理解了 OpenSSH 服务器安全配置。服务器目前禁用了 root 登录（遵循安全最佳实践），并启用了密码验证（提供了灵活性，但可能需要针对生产环境进行额外的安全考量）。你还验证了基于 SSH 密钥的身份验证作为一种更安全的身份验证方法可以正常工作。
