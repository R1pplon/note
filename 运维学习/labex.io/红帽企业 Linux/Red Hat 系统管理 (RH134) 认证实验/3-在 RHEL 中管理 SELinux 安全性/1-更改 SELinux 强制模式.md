# 更改 SELinux 强制模式

在这一步中，你将学习如何管理 SELinux 模式，包括临时和永久地更改。SELinux（安全增强型 Linux）是一种安全机制，它为 Linux 内核提供强制访问控制（MAC）。它定义了进程、文件和其他系统资源的访问权限。

SELinux 在三种主要模式下运行：

- **Enforcing（强制）**：SELinux 策略被强制执行。策略拒绝的访问将被阻止并记录。这是默认且最安全的模式。
- **Permissive（许可）**：SELinux 策略不被强制执行。策略拒绝的访问被记录，但不会被阻止。此模式对于故障排除和测试新策略很有用。
- **Disabled（禁用）**：SELinux 被关闭。没有加载或强制执行策略。通常不建议在生产系统中使用此模式。

你将练习使用命令行工具和修改配置文件来更改 SELinux 模式。

首先，让我们检查当前的 SELinux 执行模式。

1. **检查当前的 SELinux 执行模式。**

    你可以使用 `getenforce` 命令快速查看当前的 SELinux 模式。

    ```bash
    getenforce
    ```

    你应该看到输出为 `Enforcing`，这表明 SELinux 当前正在执行其策略。

    ```plaintext
    Enforcing
    ```

2. **临时将 SELinux 模式更改为 `permissive`。**

    `setenforce` 命令允许你在运行时更改 SELinux 模式。值 `0` 将模式设置为 `permissive`，而 `1` 将其设置为 `enforcing`。此更改是临时的，不会在重新启动后保留。

    ```bash
    sudo setenforce 0
    ```

    现在，再次使用 `getenforce` 验证更改。

    ```bash
    getenforce
    ```

    输出现在应该是 `Permissive`。

    ```plaintext
    Permissive
    ```

3. **临时将 SELinux 模式改回 `enforcing`。**

    要恢复临时更改，请使用 `setenforce 1`。

    ```bash
    sudo setenforce 1
    ```

    再次验证模式。

    ```bash
    getenforce
    ```

    输出应该再次为 `Enforcing`。

    ```plaintext
    Enforcing
    ```

4. **永久将默认 SELinux 模式更改为 `permissive`。**

    要使 SELinux 模式更改在重新启动后仍然有效，你需要修改 `/etc/selinux/config` 文件。此文件定义了系统的默认 SELinux 模式。

    使用 `nano` 打开配置文件。

    ```bash
    sudo nano /etc/selinux/config
    ```

    在 `nano` 编辑器中，找到以 `SELINUX=` 开头的行，并将其值从 `enforcing` 更改为 `permissive`。

    ```
    # This file controls the state of SELinux on the system.
    # SELINUX= can take one of these three values:
    #     enforcing - SELinux security policy is enforced.
    #     permissive - SELinux prints warnings instead of enforcing.
    #     disabled - No SELinux policy is loaded.
    SELINUX=permissive
    # SELINUXTYPE= can take one of these three values:
    #     targeted - Targeted processes are protected,
    #                for the majority of users.
    #     minimum - Modification of targeted policy
    #               uses current settings and adds to it.
    #     mls - Multi Level Security protection.
    SELINUXTYPE=targeted
    ```

    按 `Ctrl+X` 退出，然后按 `Y` 确认保存，最后按 `Enter` 写入同一文件。

    保存文件后，你可以使用 `grep` 确认配置文件中的更改。

    ```bash
    grep '^SELINUX' /etc/selinux/config
    ```

    输出应该显示 `SELINUX=permissive`。

    ```plaintext
    SELINUX=permissive
    SELINUXTYPE=targeted
    ```

    **重要提示**：更改 `/etc/selinux/config` 不会立即更改活动的 SELinux 模式。它仅设置将在下次系统重新启动后应用的模式。要查看当前活动模式，你仍然需要使用 `getenforce`。

    ```bash
    getenforce
    ```

    它应该仍然显示 `Enforcing`，因为系统尚未重新启动。

    ```plaintext
    Enforcing
    ```

5. **在配置文件中将默认 SELinux 模式改回 `enforcing`。**

    现在，让我们将持久模式改回 `enforcing`。这是 SELinux 推荐且最安全的安全设置。

    再次打开配置文件。

    ```bash
    sudo nano /etc/selinux/config
    ```

    将 `SELINUX=` 参数改回 `enforcing`。

    ```
    # This file controls the state of SELinux on the system.
    # SELINUX= can take one of these three values:
    #     enforcing - SELinux security policy is enforced.
    #     permissive - SELinux prints warnings instead of enforcing.
    #     disabled - No SELinux policy is loaded.
    SELINUX=enforcing
    # SELINUXTYPE= can take one of these three values:
    #     targeted - Targeted processes are protected,
    #                for the majority of users.
    #     minimum - Modification of targeted policy
    #               uses current settings and adds to it.
    #     mls - Multi Level Security protection.
    SELINUXTYPE=targeted
    ```

    保存并退出文件（`Ctrl+X`，`Y`，`Enter`）。

    确认配置文件中的更改。

    ```bash
    grep '^SELINUX' /etc/selinux/config
    ```

    输出现在应该显示 `SELINUX=enforcing`。

    ```plaintext
    SELINUX=enforcing
    SELINUXTYPE=targeted
    ```

    此时，系统的活动 SELinux 模式仍然是 `Enforcing`（如果你在步骤 4 之后没有重新启动），并且持久设置也是 `Enforcing`。
