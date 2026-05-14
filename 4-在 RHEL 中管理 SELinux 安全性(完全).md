## 介绍

在这个实验（Lab）中，你将获得在 RHEL 中管理 SELinux 安全性的实践经验。你将学习如何临时和永久地更改 SELinux 的执行模式，并使用自定义的 SELinux 文件上下文来配置 Apache。该实验（Lab）还涵盖了使用布尔值调整用户主目录的 SELinux 策略，并提供了针对 Apache Web 服务器和自定义 Web 内容的 SELinux 拒绝访问进行故障排除和解决的实用步骤。

## 更改 SELinux 强制模式

在这一步中，你将学习如何管理 SELinux 模式，包括临时和永久地更改。SELinux（安全增强型 Linux）是一种安全机制，它为 Linux 内核提供强制访问控制（MAC）。它定义了进程、文件和其他系统资源的访问权限。

SELinux 在三种主要模式下运行：

- **Enforcing（强制）**：SELinux 策略被强制执行。策略拒绝的访问将被阻止并记录。这是默认且最安全的模式。
- **Permissive（许可）**：SELinux 策略不被强制执行。策略拒绝的访问被记录，但不会被阻止。此模式对于故障排除和测试新策略很有用。
- **Disabled（禁用）**：SELinux 被关闭。没有加载或强制执行策略。通常不建议在生产系统中使用此模式。

你将练习使用命令行工具和修改配置文件来更改 SELinux 模式。

首先，让我们检查当前的 SELinux 执行模式。

1. **检查当前的 SELinux 执行模式。**
    
    你可以使用 `getenforce` 命令快速查看当前的 SELinux 模式。
    
    ```bash
    getenforce
    ```
    
    你应该看到输出为 `Enforcing`，这表明 SELinux 当前正在执行其策略。
    
    ```plaintext
    Enforcing
    ```
    
2. **临时将 SELinux 模式更改为 `permissive`。**
    
    `setenforce` 命令允许你在运行时更改 SELinux 模式。值 `0` 将模式设置为 `permissive`，而 `1` 将其设置为 `enforcing`。此更改是临时的，不会在重新启动后保留。
    
    ```bash
    sudo setenforce 0
    ```
    
    现在，再次使用 `getenforce` 验证更改。
    
    ```bash
    getenforce
    ```
    
    输出现在应该是 `Permissive`。
    
    ```plaintext
    Permissive
    ```
    
3. **临时将 SELinux 模式改回 `enforcing`。**
    
    要恢复临时更改，请使用 `setenforce 1`。
    
    ```bash
    sudo setenforce 1
    ```
    
    再次验证模式。
    
    ```bash
    getenforce
    ```
    
    输出应该再次为 `Enforcing`。
    
    ```plaintext
    Enforcing
    ```
    
4. **永久将默认 SELinux 模式更改为 `permissive`。**
    
    要使 SELinux 模式更改在重新启动后仍然有效，你需要修改 `/etc/selinux/config` 文件。此文件定义了系统的默认 SELinux 模式。
    
    使用 `nano` 打开配置文件。
    
    ```bash
    sudo nano /etc/selinux/config
    ```
    
    在 `nano` 编辑器中，找到以 `SELINUX=` 开头的行，并将其值从 `enforcing` 更改为 `permissive`。
    
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
    
    按 `Ctrl+X` 退出，然后按 `Y` 确认保存，最后按 `Enter` 写入同一文件。
    
    保存文件后，你可以使用 `grep` 确认配置文件中的更改。
    
    ```bash
    grep '^SELINUX' /etc/selinux/config
    ```
    
    输出应该显示 `SELINUX=permissive`。
    
    ```plaintext
    SELINUX=permissive
    SELINUXTYPE=targeted
    ```
    
    **重要提示**：更改 `/etc/selinux/config` 不会立即更改活动的 SELinux 模式。它仅设置将在下次系统重新启动后应用的模式。要查看当前活动模式，你仍然需要使用 `getenforce`。
    
    ```bash
    getenforce
    ```
    
    它应该仍然显示 `Enforcing`，因为系统尚未重新启动。
    
    ```plaintext
    Enforcing
    ```
    
5. **在配置文件中将默认 SELinux 模式改回 `enforcing`。**
    
    现在，让我们将持久模式改回 `enforcing`。这是 SELinux 推荐且最安全的安全设置。
    
    再次打开配置文件。
    
    ```bash
    sudo nano /etc/selinux/config
    ```
    
    将 `SELINUX=` 参数改回 `enforcing`。
    
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
    
    输出现在应该显示 `SELINUX=enforcing`。
    
    ```plaintext
    SELINUX=enforcing
    SELINUXTYPE=targeted
    ```
    
    此时，系统的活动 SELinux 模式仍然是 `Enforcing`（如果你在步骤 4 之后没有重新启动），并且持久设置也是 `Enforcing`。

## 使用自定义 SELinux 文件上下文配置 Apache

在这一步中，你将学习如何配置 Apache，以便从非标准目录提供 Web 内容并管理其 SELinux 文件上下文。默认情况下，SELinux 策略限制 Apache (httpd) 仅从特定目录（主要是 `/var/www/html`）提供文件。如果你将 Web 内容放置在不同的位置，即使文件系统权限正确，SELinux 也会阻止 Apache 访问它。这就是 SELinux 文件上下文发挥作用的地方。

SELinux 文件上下文是应用于文件或目录的标签，用于定义其安全属性。为了让 Apache 从自定义位置提供内容，该位置及其内容必须具有正确的 SELinux 上下文，通常是 `httpd_sys_content_t`。你将使用 `semanage fcontext` 定义一个持久规则，并使用 `restorecon` 应用它。

首先，你需要安装 Apache HTTP 服务器。

1. **安装 Apache HTTP 服务器。**
    
    使用 `dnf` 包管理器安装 `httpd` 包。
    
    ```bash
    sudo dnf install -y httpd
    ```
    
    你应该看到输出，表明成功安装了 `httpd` 包及其依赖项。
    
2. **为 Web 内容创建一个自定义目录和一个 `index.html` 文件。**
    
    你将创建一个名为 `/custom` 的新目录，并在其中放置一个简单的 `index.html` 文件。这将是你的非标准 Web 文档根目录。
    
    ```bash
    sudo mkdir /custom
    echo 'This is custom web content.' | sudo tee /custom/index.html
    ```
    
    验证 `index.html` 文件的内容。
    
    ```bash
    cat /custom/index.html
    ```
    
    ```plaintext
    This is custom web content.
    ```
    
3. **配置 Apache 以使用新的文档根目录。**
    
    Apache 的主配置文件是 `/etc/httpd/conf/httpd.conf`。你需要编辑此文件，将 Apache 指向你的新 `/custom` 目录，而不是默认的 `/var/www/html`。
    
    使用 `nano` 打开配置文件。
    
    ```bash
    sudo nano /etc/httpd/conf/httpd.conf
    ```
    
    在编辑器中，找到行 `DocumentRoot "/var/www/html"` 和 `<Directory "/var/www/html">`。将所有出现的 `/var/www/html` 更改为 `/custom`。
    
    修改后的相关部分应如下所示：
    
    ```apacheconf
    #
    # DocumentRoot: The directory out of which you will serve your
    # documents. By default, all requests are taken from this directory, but
    # symbolic links and aliases may be used to point to other locations.
    #
    DocumentRoot "/custom"
    
    #
    # Relax access to content within /var/www.
    #
    <Directory "/custom">
        AllowOverride None
        # Allow open access:
        Require all granted
    </Directory>
    ```
    
    保存并退出文件（`Ctrl+X`，`Y`，`Enter`）。
    
4. **启动并启用 Apache Web 服务。**
    
    修改配置后，你需要重新启动 `httpd` 服务以使更改生效。
    
    ```bash
    sudo systemctl restart httpd
    ```
    
    要验证 Apache 是否正在运行，你可以检查 `httpd` 服务状态。
    
    ```bash
    sudo systemctl status httpd
    ```
    
    你应该看到 `httpd` 服务状态为 `active (running)`。
    
    ```plaintext
    ● httpd.service - The Apache HTTP Server
         Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
         Active: active (running) since Thu 2026-05-14 15:31:15 CST; 1min 12s ago
     Invocation: 7bf50031cf1f4434932efbac2fe2e8ac
           Docs: man:httpd.service(8)
       Main PID: 3060 (httpd)
         Status: "Total requests: 0; Idle/Busy workers 100/0;Requests/sec: 0; Bytes served/sec:   0 B/sec"
          Tasks: 177 (limit: 22799)
         Memory: 13.7M (peak: 13.9M)
            CPU: 151ms
         CGroup: /system.slice/httpd.service
                 ├─3060 /usr/sbin/httpd -DFOREGROUND
                 ├─3061 /usr/sbin/httpd -DFOREGROUND
                 ├─3062 /usr/sbin/httpd -DFOREGROUND
                 ├─3063 /usr/sbin/httpd -DFOREGROUND
                 └─3064 /usr/sbin/httpd -DFOREGROUND
    
    May 14 15:31:15 localhost.localdomain systemd[1]: Starting httpd.service - The Apache HTTP Server...
    May 14 15:31:15 localhost.localdomain (httpd)[3060]: httpd.service: Referenced but unset environment variable evaluates to an empty >
    May 14 15:31:15 localhost.localdomain httpd[3060]: AH00558: httpd: Could not reliably determine the server's fully qualified domain >
    May 14 15:31:15 localhost.localdomain httpd[3060]: Server configured, listening on: port 80
    May 14 15:31:15 localhost.localdomain systemd[1]: Started httpd.service - The Apache HTTP Server.
    ```
    
5. **尝试访问网页。**
    
    现在，尝试使用 `curl` 访问你的网页。由于你在同一台机器上，你可以使用 `localhost`。
    
    ```bash
    curl http://localhost/index.html
    ```
    
    即使你在前面的步骤中已经配置了文件系统权限并修改了 Apache 配置文件，你仍然会收到一个 **403 Forbidden** 错误。这表明访问被拒绝。
    
    ```plaintext
    <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
    <html><head>
    <title>403 Forbidden</title>
    </head><body>
    <h1>Forbidden</h1>
    <p>You don't have permission to access this resource.</p>
    </body></html>
    ```
    **为什么会被拒绝？** 这正是 SELinux 在起作用。默认情况下，Apache 只被允许读取带有特定 SELinux 标签的文件。由于你的 `/custom` 目录不在默认的 Web 目录中，它没有正确的标签，因此 SELinux 阻止了 Apache 的读取操作。
    
    
6. **检查自定义目录的当前 SELinux 上下文。**
    
    使用 `ls -Z` 命令查看你的 `/custom` 目录和 `index.html` 文件的 SELinux 上下文。
    
    ```bash
    ls -Zd /custom /custom/index.html
    ```
    
    你会注意到它们具有 `default_t` 上下文。在根目录下新创建的目录通常会获得这个通用标签，但这**不是** Apache Web 内容被允许访问的上下文。
    
    ```plaintext
    unconfined_u:object_r:default_t:s0 /custom
    unconfined_u:object_r:default_t:s0 /custom/index.html
    ```
    
    将其与默认的 Apache 文档根目录进行比较：
    
    ```bash
    ls -Zd /var/www/html
    ```
    
    你将看到 `/var/www/html` 具有 `httpd_sys_content_t` 上下文。这是你需要应用于你的自定义目录的上下文。
    
    ```plaintext
    system_u:object_r:httpd_sys_content_t:s0 /var/www/html
    ```
    
7. **为 `/custom` 定义一个持久的 SELinux 文件上下文规则。**
    
    `semanage fcontext` 命令用于管理 SELinux 文件上下文映射规则。`-a` 选项添加一个新规则，`-t` 指定目标类型，正则表达式 `'/custom(/.*)?'` 匹配 `/custom` 目录本身以及其中的所有文件和子目录。
    
    ```bash
    sudo semanage fcontext -a -t httpd_sys_content_t '/custom(/.*)?'
    ```
    
    此命令将规则添加到 SELinux 策略中，但它不会立即更改现有文件的上下文。
    
8. **将新的 SELinux 上下文应用于文件。**
    
    `restorecon` 命令用于将文件和目录的 SELinux 上下文恢复为其默认值，这些默认值由策略定义。`-R` 选项递归地应用更改，`-v` 提供详细输出。
    
    ```bash
    sudo restorecon -Rv /custom
    ```
    
    你应该看到输出，表明 `/custom` 和 `/custom/index.html` 的上下文已被重新标记。
    
    ```plaintext
    Relabeled /custom from system_u:object_r:root_t:s0 to system_u:object_r:httpd_sys_content_t:s0
    Relabeled /custom/index.html from system_u:object_r:root_t:s0 to system_u:object_r:httpd_sys_content_t:s0
    ```
    
    再次使用 `ls -Z` 验证上下文。
    
    ```bash
    ls -Zd /custom /custom/index.html
    ```
    
    它们现在应该具有 `httpd_sys_content_t` 上下文。
    
    ```plaintext
    system_u:object_r:httpd_sys_content_t:s0 /custom
    system_u:object_r:httpd_sys_content_t:s0 /custom/index.html
    ```
    
9. **再次访问网页。**
    
    现在 SELinux 上下文已正确，再次尝试使用 `curl` 访问网页。
    
    ```bash
    curl http://localhost/index.html
    ```
    
    你现在应该看到你的 `index.html` 文件的内容。
    
    ```plaintext
    This is custom web content.
    ```
    
    最后，停止 Apache HTTP 服务器进程。
    
    ```bash
    sudo pkill httpd
    ```
    
    验证没有 `httpd` 进程正在运行。
    
    ```bash
    ps aux | grep httpd
    ```
    
    你应该只看到 `grep` 进程本身。
    
    ```plaintext
    labex     ... grep httpd
    ```

## 使用布尔值调整 SELinux 策略以管理用户主目录

在这一步中，你将学习如何使用布尔值调整 SELinux 策略，以允许 Apache 从用户的主目录提供 Web 内容。默认情况下，出于安全原因，SELinux 阻止 Apache 等服务访问用户主目录中的文件。但是，在某些特定情况下，例如个人网页，需要此功能。

SELinux 布尔值是 true/false 设置，允许管理员在不编写复杂的自定义策略的情况下修改 SELinux 策略的行为。它们提供了一种灵活的方式来启用或禁用某些安全功能。例如，有一个布尔值专门用于允许 Apache 访问用户主目录。

1. **启用 Apache 的用户目录功能。**
    
    Apache 有一个名为 `mod_userdir` 的模块，允许用户从其主目录中的 `public_html` 目录（例如，`~/public_html`）发布 Web 内容。此功能通常在 `/etc/httpd/conf.d/userdir.conf` 中配置。默认情况下，此功能通常被禁用。
    
    使用 `nano` 打开配置文件。
    
    ```bash
    sudo nano /etc/httpd/conf.d/userdir.conf
    ```
    
    在编辑器中，你将找到与 `UserDir` 相关的行。你需要注释掉禁用 `UserDir` 的行，并取消注释为 `public_html` 启用它的行。
    
    将：
    
    ```apacheconf
    UserDir disabled
    #UserDir public_html
    ```
    
    更改为：
    
    ```apacheconf
    #UserDir disabled
    UserDir public_html
    ```
    
    保存并退出文件（`Ctrl+X`，`Y`，`Enter`）。
    
2. **在你的主目录中创建一个 `public_html` 目录和一个 `index.html` 文件。**
    
    你将创建 `public_html` 目录并在其中放置一个 `index.html` 文件。这是你的个人 Web 内容所在的位置。
    
    ```bash
    mkdir ~/public_html
    echo 'This is labex user content.' > ~/public_html/index.html
    ```
    
    验证 `index.html` 文件的内容。
    
    ```bash
    cat ~/public_html/index.html
    ```
    
    ```plaintext
    This is labex user content.
    ```
    
    **信息**：当你创建 `~/public_html` 目录时，它会自动配置 `user_home_t` 和 `~/`（你的主目录）与 `home_dir_t` SELinux 上下文。默认情况下，由于 SELinux 策略，Apache Web 服务器进程 (`httpd_t`) 无法读取标记为 `user_home_t` 或 `home_dir_t` 的文件。
    
3. **启动 Apache Web 服务。**
    
    启动 `httpd` 服务。请记住，在此容器环境中，`systemctl` 不可用，因此你将直接启动 `httpd`。
    
    ```bash
    sudo /usr/sbin/httpd -DFOREGROUND &
    ```
    
    你可能会看到一条关于服务器完全限定域名（FQDN）的警告消息，在此实验环境中可以安全地忽略。
    
    验证 Apache 是否正在运行。
    
    ```bash
    ps aux | grep httpd
    ```
    
    ```plaintext
    root        ... /usr/sbin/httpd -DFOREGROUND
    apache      ... /usr/sbin/httpd -DFOREGROUND
    ...output omitted...
    ```
    
4. **尝试访问用户的网页并观察 SELinux 拒绝。**
    
    现在，尝试使用 `curl` 访问你的个人网页。用户目录的 URL 通常遵循 `http://localhost/~username/` 格式。
    
    ```bash
    curl http://localhost/~labex/index.html
    ```
    
    你可能会收到“禁止访问”错误，表明由于 SELinux，Apache 仍然无法访问内容。
    
    ```plaintext
    <!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
    <html><head>
    <title>403 Forbidden</title>
    </head><body>
    <h1>Forbidden</h1>
    <p>You don't have permission to access /~labex/index.html
    on this server.<br />
    </p>
    </body></html>
    ```
    
5. **检查与主目录相关的 `httpd` 的 SELinux 布尔值。**
    
    `getsebool` 命令允许你查看 SELinux 布尔值的当前状态。你可以使用 `grep` 过滤输出，以查找与 `httpd` 和主目录相关的布尔值。
    
    ```bash
    sudo getsebool -a | grep httpd | grep home
    ```
    
    你应该看到 `httpd_enable_homedirs --> off`，表明此布尔值当前已禁用。
    
    ```plaintext
    httpd_enable_homedirs --> off
    ```
    
6. **永久启用 `httpd_enable_homedirs` 布尔值。**
    
    `setsebool` 命令用于更改 SELinux 布尔值的状态。`-P` 选项使更改在重新启动后仍然有效。
    
    ```bash
    sudo setsebool -P httpd_enable_homedirs on
    ```
    
    验证布尔值现在是否为 `on`。
    
    ```bash
    sudo getsebool -a | grep httpd | grep home
    ```
    
    ```plaintext
    httpd_enable_homedirs --> on
    ```
    
7. **为 home 目录设置正确的文件权限。**
    
    即使启用了 SELinux 布尔值，Apache 也需要正确的文件系统权限才能访问你的主目录和 `public_html` 目录。默认情况下，用户主目录对 Apache 用户不可访问。
    
    ```bash
    chmod 711 ~
    chmod 755 ~/public_html
    chmod 644 ~/public_html/index.html
    ```
    
8. **再次访问网页。**
    
    现在，`httpd_enable_homedirs` 布尔值已启用，并且文件权限正确，再次尝试使用 `curl` 访问你的个人网页。
    
    ```bash
    curl http://localhost/~labex/index.html
    ```
    
    你现在应该看到你的 `index.html` 文件的内容。
    
    ```plaintext
    This is labex user content.
    ```
    
    **故障排除**：即使在启用布尔值和设置文件权限后，如果仍然遇到访问问题，这表明了 Linux 安全性的多层性质。在某些环境中，其他因素，例如：
    
    - `/etc/httpd/conf.d/userdir.conf` 中的 Apache 配置指令
    - 主目录结构上的 SELinux 文件上下文
    - 其他 Apache 模块或安全设置
    
    可能需要解决。关键的学习点是理解 SELinux 布尔值如何与传统的文件权限和特定于应用程序的配置结合使用。
    
9. **停止 Apache HTTP 服务器进程。**
    
    最后，停止 Apache HTTP 服务器进程。
    
    ```bash
    sudo pkill httpd
    ```
    
    验证没有 `httpd` 进程正在运行。
    
    ```bash
    ps aux | grep httpd
    ```
    
    ```plaintext
    labex     ... grep httpd
    ```

## Apache Web 服务器 SELinux 拒绝访问故障排除

在这一步中，你将学习如何识别和排除 SELinux 安全拒绝，特别关注可能阻止 Apache Web 服务器正常运行的问题。当 SELinux 阻止一个操作时，它会记录一条“访问向量缓存”（AVC）拒绝消息。这些消息对于理解操作失败的原因以及如何解决它至关重要。

你将使用 `auditd`（Linux 审计系统守护程序）和 `sealert` 等工具来分析这些拒绝消息。`auditd` 收集系统调用和事件，包括 SELinux 拒绝，并将它们存储在 `/var/log/audit/audit.log` 中。`sealert` 工具是 `setroubleshoot-server` 包的一部分，可以解析这些日志，并为 SELinux 拒绝提供人类可读的解释和建议的解决方案。

首先，你需要确保安装了 `auditd` 和 `setroubleshoot-server`。

1. **安装 `auditd` 和 `setroubleshoot-server`。**
    
    ```bash
    sudo dnf install -y audit setroubleshoot-server
    ```
    
    你应该看到输出，表明这些软件包已成功安装。
    
2. **启动 Apache Web 服务器并创建一个有问题的文件。**
    
    为了模拟 SELinux 拒绝，你将创建一个具有不正确 SELinux 上下文的文件，并尝试使用 Apache 提供它。
    
    首先，确保 Apache 正在运行。
    
    ```bash
    sudo /usr/sbin/httpd -DFOREGROUND &
    ```
    
    现在，创建一个新目录并在其中创建一个 `index.html` 文件。这次，你将故意为该文件设置不正确的 SELinux 上下文以触发拒绝。
    
    ```bash
    sudo mkdir /testweb
    echo 'This is a test page.' | sudo tee /testweb/index.html
    ```
    
    默认情况下，`/testweb/index.html` 可能会具有 `root_t` 上下文。让我们确认一下。
    
    ```bash
    ls -Z /testweb/index.html
    ```
    
    ```plaintext
    system_u:object_r:root_t:s0 /testweb/index.html
    ```
    
    现在，让我们配置 Apache 以从 `/testweb` 提供服务。打开 `/etc/httpd/conf/httpd.conf`。
    
    ```bash
    sudo nano /etc/httpd/conf/httpd.conf
    ```
    
    将 `DocumentRoot` 和 `<Directory>` 指令更改为 `/testweb`。
    
    ```apacheconf
    DocumentRoot "/testweb"
    
    <Directory "/testweb">
        AllowOverride None
        Require all granted
    </Directory>
    ```
    
    保存并退出（`Ctrl+X`，`Y`，`Enter`）。
    
    重新启动 Apache 以应用配置更改。由于你在容器中，你需要杀死旧进程并启动一个新进程。
    
    ```bash
    sudo pkill httpd
    sudo /usr/sbin/httpd -DFOREGROUND &
    ```
    
3. **尝试访问网页。**
    
    尝试使用 `curl` 访问网页。
    
    ```bash
    curl http://localhost/index.html
    ```
    
    **重要提示**：在此环境中，你可能会发现即使使用 `root_t` 上下文，网页也可以访问，这与我们在步骤 2 中观察到的类似。这表明，虽然 SELinux 正在执行，但 `root_t` 上下文具有比更严格的上下文更广泛的权限。
    
    ```plaintext
    This is a test page.
    ```
    
    但是，为了学习 SELinux 故障排除技术，我们将继续进行，就好像存在拒绝一样。在更严格的 SELinux 环境或不同的策略配置下，访问具有不适当上下文的文件确实会生成拒绝。
    
4. **了解如何使用 `ausearch` 调查 SELinux 拒绝。**
    
    `ausearch` 命令用于查询审计日志。你可以搜索今天发生的 SELinux AVC 拒绝（`-m AVC`）（`-ts today`）。
    
    ```bash
    sudo ausearch -m AVC -ts today
    ```
    
    **注意**：由于在我们的环境中可以访问网页，因此你可能不会看到与此特定测试相关的任何最近的 AVC 拒绝。但是，如果存在拒绝，此命令通常会输出详细的审计日志条目。在典型的拒绝场景中，你将查找与 `httpd` 和 `/testweb/index.html` 相关的条目。
    
    典型的 AVC 拒绝条目如下所示：
    
    ```plaintext
    ----
    time->...
    type=AVC msg=audit(...): avc:  denied  { getattr } for  pid=... comm="httpd" path="/testweb/index.html" dev="overlay" ino=... scontext=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:root_t:s0 tclass=file permissive=0
    ...output omitted...
    ```
    
    AVC 拒绝中的关键部分将是：
    
    - `denied { getattr }`：被拒绝的操作（获取文件的属性）。
    - `comm="httpd"`：被拒绝的进程（Apache HTTP 服务器）。
    - `path="/testweb/index.html"`：被访问的文件。
    - `scontext=system_u:system_r:httpd_t:s0`：源的 SELinux 上下文（Apache）。
    - `tcontext=system_u:object_r:root_t:s0`：目标的 SELinux 上下文（你的 `index.html` 文件）。
    - `tclass=file`：目标的类型（文件）。
    
    此输出清楚地表明，`httpd_t`（Apache）被拒绝了对具有 `default_t` 上下文的文件的 `getattr` 访问。
    
5. **了解如何使用 `sealert` 进行 SELinux 分析。**
    
    `sealert` 可以解析审计日志并提供更友好的信息。你可以运行 `sealert -a` 来分析所有最近的拒绝，或者如果你有来自 `/var/log/messages` 中的 `setroubleshoot` 消息的特定 UUID，则可以使用 `sealert -l <UUID>`。
    
    ```bash
    sudo sealert -a /var/log/audit/audit.log
    ```
    
    **注意**：由于我们在此环境中尚未遇到实际拒绝，因此运行 `sealert` 可能不会显示与我们的 `/testweb` 示例相关的结果。但是，在发生 SELinux 拒绝的情况下，`sealert` 将分析审计日志并提供摘要。
    
    `sealert` 的典型输出，用于 httpd 上下文问题，将如下所示：
    
    ```plaintext
    SELinux is preventing /usr/sbin/httpd from getattr access on the file /testweb/index.html.
    
    ***** Plugin catchall_labels (83.8 confidence) suggests *******************
    If you want to allow httpd to have getattr access on the index.html file
    Then you need to change the label on /testweb/index.html
    Do # semanage fcontext -a -t FILE_TYPE '/testweb/index.html'
    where FILE_TYPE is one of the following:
    httpd_sys_content_t, httpd_sys_script_exec_t, httpd_unconfined_script_exec_t, ...
    
    ***** Plugin httpd_can_network_connect (93.8 confidence) suggests *********
    If you want to allow httpd to connect to the network (for example, to a database)
    Then you must set the httpd_can_network_connect boolean to on.
    Do # setsebool -P httpd_can_network_connect on
    ...output omitted...
    ```
    
    在实际拒绝场景中，`sealert` 输出将非常有帮助。它将明确说明问题并提出解决方案，例如使用 `semanage fcontext -a -t FILE_TYPE '/testweb/index.html'` 更改标签，并将 `httpd_sys_content_t` 列为合适的 `FILE_TYPE`。
    
    最后，停止 Apache HTTP 服务器进程。
    
    ```bash
    sudo pkill httpd
    ```
    
    验证没有 `httpd` 进程正在运行。
    
    ```bash
    ps aux | grep httpd
    ```
    
    ```plaintext
    labex     ... grep httpd
    ```

## 解决自定义 Web 内容的 SELinux 问题

在最后一步中，你将应用从之前的故障排除练习中获得的知识，以解决阻止 Apache 从 `/testweb` 目录提供内容的 SELinux 拒绝。你将使用 `semanage fcontext` 为你的自定义 Web 内容定义正确的 SELinux 上下文，并使用 `restorecon` 应用它。

此过程加强了对 SELinux 上下文如何工作以及如何为 Apache 等服务正确配置它们的理解。

1. **确保 Apache 正在运行并且配置已到位。**
    
    首先，确保 Apache 已配置为从 `/testweb` 提供服务并且正在运行。如果你在上一步中停止了 Apache，请再次启动它。
    
    ```bash
    sudo /usr/sbin/httpd -DFOREGROUND &
    ```
    
    验证 `/etc/httpd/conf/httpd.conf` 中的 `DocumentRoot` 是否设置为 `/testweb`。如果不是，请按照上一步中的操作进行修改。
    
    ```bash
    grep "DocumentRoot" /etc/httpd/conf/httpd.conf
    ```
    
    ```plaintext
    DocumentRoot "/testweb"
    ```
    
    此外，确认 `/testweb/index.html` 存在并具有 `root_t` 上下文。
    
    ```bash
    ls -Z /testweb/index.html
    ```
    
    ```plaintext
    system_u:object_r:root_t:s0 /testweb/index.html
    ```
    
2. **访问网页以确认当前行为。**
    
    让我们验证当前使用 `root_t` 上下文是否可以访问网页。
    
    ```bash
    curl http://localhost/index.html
    ```
    
    正如我们之前看到的，即使使用 `root_t` 上下文，也可以访问该页面。
    
    ```plaintext
    This is a test page.
    ```
    
    虽然这有效，但我们将继续演示 Web 内容的正确 SELinux 配置。
    
3. **为 `/testweb` 定义正确的 SELinux 文件上下文。**
    
    Apache 提供的 Web 内容的正确 SELinux 上下文是 `httpd_sys_content_t`。你需要使用 `semanage fcontext` 添加一个持久规则。
    
    ```bash
    sudo semanage fcontext -a -t httpd_sys_content_t '/testweb(/.*)?'
    ```
    
    此命令告诉 SELinux，`/testweb` 内的任何文件或目录（包括 `/testweb` 本身）都应标记为 `httpd_sys_content_t`。
    
4. **将新的 SELinux 上下文应用于文件。**
    
    定义规则后，你必须使用 `restorecon` 将其应用于现有文件。
    
    ```bash
    sudo restorecon -Rv /testweb
    ```
    
    你应该看到输出，表明上下文已被重新标记。
    
    ```plaintext
    Relabeled /testweb from system_u:object_r:root_t:s0 to system_u:object_r:httpd_sys_content_t:s0
    Relabeled /testweb/index.html from system_u:object_r:root_t:s0 to system_u:object_r:httpd_sys_content_t:s0
    ```
    
    再次使用 `ls -Z` 验证上下文。
    
    ```bash
    ls -Z /testweb/index.html
    ```
    
    ```plaintext
    system_u:object_r:httpd_sys_content_t:s0 /testweb/index.html
    ```
    
5. **再次访问网页以确认正确的配置。**
    
    现在，SELinux 上下文已根据最佳实践正确应用，再次尝试使用 `curl` 访问网页。
    
    ```bash
    curl http://localhost/index.html
    ```
    
    内容应该仍然可以访问，并且现在已使用推荐的 SELinux 上下文正确配置。
    
    ```plaintext
    This is a test page.
    ```
    
    这表明，虽然 `root_t` 上下文在此环境中可能有效，但使用正确的 `httpd_sys_content_t` 上下文遵循 SELinux 最佳实践，并确保跨不同安全配置的兼容性。
    
    最后，停止 Apache HTTP 服务器进程。
    
    ```bash
    sudo pkill httpd
    ```
    
    验证没有 `httpd` 进程正在运行。
    
    ```bash
    ps aux | grep httpd
    ```
    
    ```plaintext
    labex     ... grep httpd
    ```

## 总结

在这个实验中，你学习了如何在 RHEL 中管理 SELinux 安全性。你首先通过理解和实践如何更改 SELinux 强制模式开始，既可以使用 `setenforce` 临时更改，也可以通过修改 `/etc/selinux/config` 永久更改。这包括使用 `getenforce` 验证当前模式，并理解 Enforcing（强制）、Permissive（许可）和 Disabled（禁用）模式的含义。

此外，你获得了使用 `semanage fcontext` 和 `restorecon` 配置 Apache 与自定义 SELinux 文件上下文的实践经验，以确保 Web 服务器的正常运行。你还学习了通过使用 `setsebool` 启用特定的 SELinux 布尔值来调整用户主目录的 SELinux 策略。最后，该实验涵盖了 SELinux 拒绝的基本故障排除技术，特别是针对 Apache Web 服务器，通过使用 `ausearch` 和 `audit2allow` 分析审计日志来识别和解决自定义 Web 内容的访问问题。