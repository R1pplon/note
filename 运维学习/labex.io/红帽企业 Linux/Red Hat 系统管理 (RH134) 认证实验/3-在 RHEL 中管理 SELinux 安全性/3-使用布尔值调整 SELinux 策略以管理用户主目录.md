# 使用布尔值调整 SELinux 策略以管理用户主目录

在这一步中，你将学习如何使用布尔值调整 SELinux 策略，以允许 Apache 从用户的主目录提供 Web 内容。默认情况下，出于安全原因，SELinux 阻止 Apache 等服务访问用户主目录中的文件。但是，在某些特定情况下，例如个人网页，需要此功能。

SELinux 布尔值是 true/false 设置，允许管理员在不编写复杂的自定义策略的情况下修改 SELinux 策略的行为。它们提供了一种灵活的方式来启用或禁用某些安全功能。例如，有一个布尔值专门用于允许 Apache 访问用户主目录。

1. **启用 Apache 的用户目录功能。**

    Apache 有一个名为 `mod_userdir` 的模块，允许用户从其主目录中的 `public_html` 目录（例如，`~/public_html`）发布 Web 内容。此功能通常在 `/etc/httpd/conf.d/userdir.conf` 中配置。默认情况下，此功能通常被禁用。

    使用 `nano` 打开配置文件。

    ```bash
    sudo nano /etc/httpd/conf.d/userdir.conf
    ```

    在编辑器中，你将找到与 `UserDir` 相关的行。你需要注释掉禁用 `UserDir` 的行，并取消注释为 `public_html` 启用它的行。

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

2. **在你的主目录中创建一个 `public_html` 目录和一个 `index.html` 文件。**

    你将创建 `public_html` 目录并在其中放置一个 `index.html` 文件。这是你的个人 Web 内容所在的位置。

    ```bash
    mkdir ~/public_html
    echo 'This is labex user content.' > ~/public_html/index.html
    ```

    验证 `index.html` 文件的内容。

    ```bash
    cat ~/public_html/index.html
    ```

    ```plaintext
    This is labex user content.
    ```

    **信息**：当你创建 `~/public_html` 目录时，它会自动配置 `user_home_t` 和 `~/`（你的主目录）与 `home_dir_t` SELinux 上下文。默认情况下，由于 SELinux 策略，Apache Web 服务器进程 (`httpd_t`) 无法读取标记为 `user_home_t` 或 `home_dir_t` 的文件。

3. **启动 Apache Web 服务。**

    启动 `httpd` 服务。请记住，在此容器环境中，`systemctl` 不可用，因此你将直接启动 `httpd`。

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

    现在，尝试使用 `curl` 访问你的个人网页。用户目录的 URL 通常遵循 `http://localhost/~username/` 格式。

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

5. **检查与主目录相关的 `httpd` 的 SELinux 布尔值。**

    `getsebool` 命令允许你查看 SELinux 布尔值的当前状态。你可以使用 `grep` 过滤输出，以查找与 `httpd` 和主目录相关的布尔值。

    ```bash
    sudo getsebool -a | grep httpd | grep home
    ```

    你应该看到 `httpd_enable_homedirs --> off`，表明此布尔值当前已禁用。

    ```plaintext
    httpd_enable_homedirs --> off
    ```

6. **永久启用 `httpd_enable_homedirs` 布尔值。**

    `setsebool` 命令用于更改 SELinux 布尔值的状态。`-P` 选项使更改在重新启动后仍然有效。

    ```bash
    sudo setsebool -P httpd_enable_homedirs on
    ```

    验证布尔值现在是否为 `on`。

    ```bash
    sudo getsebool -a | grep httpd | grep home
    ```

    ```plaintext
    httpd_enable_homedirs --> on
    ```

7. **为 home 目录设置正确的文件权限。**

    即使启用了 SELinux 布尔值，Apache 也需要正确的文件系统权限才能访问你的主目录和 `public_html` 目录。默认情况下，用户主目录对 Apache 用户不可访问。

    ```bash
    chmod 711 ~
    chmod 755 ~/public_html
    chmod 644 ~/public_html/index.html
    ```

8. **再次访问网页。**

    现在，`httpd_enable_homedirs` 布尔值已启用，并且文件权限正确，再次尝试使用 `curl` 访问你的个人网页。

    ```bash
    curl http://localhost/~labex/index.html
    ```

    你现在应该看到你的 `index.html` 文件的内容。

    ```plaintext
    This is labex user content.
    ```

    **故障排除**：即使在启用布尔值和设置文件权限后，如果仍然遇到访问问题，这表明了 Linux 安全性的多层性质。在某些环境中，其他因素，例如：

    - `/etc/httpd/conf.d/userdir.conf` 中的 Apache 配置指令
    - 主目录结构上的 SELinux 文件上下文
    - 其他 Apache 模块或安全设置

    可能需要解决。关键的学习点是理解 SELinux 布尔值如何与传统的文件权限和特定于应用程序的配置结合使用。

9. **停止 Apache HTTP 服务器进程。**

    最后，停止 Apache HTTP 服务器进程。

    ```bash
    sudo pkill httpd
    ```

    验证没有 `httpd` 进程正在运行。

    ```bash
    ps aux | grep httpd
    ```

    ```plaintext
    labex     ... grep httpd
    ```
