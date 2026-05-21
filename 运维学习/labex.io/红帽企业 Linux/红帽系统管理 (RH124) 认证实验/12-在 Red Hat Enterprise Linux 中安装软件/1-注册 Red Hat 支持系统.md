# 注册 Red Hat 支持系统

在本步骤中，你将学习如何使用 `subscription-manager` 命令注册你的系统以获取 Red Hat 支持。虽然在这个模拟环境中无法获得完整的 Red Hat 订阅，但理解 `subscription-manager` 命令对于管理 Red Hat Enterprise Linux 系统上的软件至关重要。该命令允许你将系统注册到 Red Hat，附加订阅，并访问 Red Hat 的内容分发网络以获取软件包和更新。

首先，让我们尝试使用占位符用户名注册系统。这将演示该命令的用法，即使实际注册需要有效的 Red Hat 凭据。

```bash
sudo subscription-manager register --username labex
```

系统会提示你输入密码。由于这是一个模拟环境，你可以输入任何密码，或者如果系统允许，只需按回车键即可。该命令可能无法连接到 Red Hat 的订阅服务，这在该实验环境中是预期的。重要的是要理解该命令的语法及其预期用途。

```plaintext
Registering to: subscription.rhsm.redhat.com:443/subscription
Password:
Invalid username or password. To create a login, please visit https://www.redhat.com/wapps/ugc/register.html (HTTP error code 401: Unauthorized)
```

最后，让我们看看如何查看已消耗的订阅。此命令显示当前附加到系统的订阅。

```bash
sudo subscription-manager list --consumed
```

由于系统未注册，你将看到一条消息，指示没有已消耗的订阅。

```plaintext
No consumed subscription pools were found.
```

接下来，让我们探索如何通常列出你的 Red Hat 帐户的可用订阅。此命令将显示与你的 Red Hat 帐户相关的各种订阅池。

```bash
sudo subscription-manager list --available
```

由于系统尚未注册，你将看到一条错误消息，指示首先需要注册。

```plaintext
This system is not yet registered. Try 'subscription-manager register --help' for more information.
```

这个练习演示了 `subscription-manager` 命令在注册和查看订阅信息方面的基本用法。虽然在这个模拟环境中功能有限，但这些命令对于管理 RHEL 系统上的软件访问至关重要。
