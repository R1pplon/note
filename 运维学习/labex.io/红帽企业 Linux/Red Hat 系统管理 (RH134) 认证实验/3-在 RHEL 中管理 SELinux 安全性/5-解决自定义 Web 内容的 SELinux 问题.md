# 解决自定义 Web 内容的 SELinux 问题

在最后一步中，你将应用从之前的故障排除练习中获得的知识，以解决阻止 Apache 从 `/testweb` 目录提供内容的 SELinux 拒绝。你将使用 `semanage fcontext` 为你的自定义 Web 内容定义正确的 SELinux 上下文，并使用 `restorecon` 应用它。

此过程加强了对 SELinux 上下文如何工作以及如何为 Apache 等服务正确配置它们的理解。

1. **确保 Apache 正在运行并且配置已到位。**

    首先，确保 Apache 已配置为从 `/testweb` 提供服务并且正在运行。如果你在上一步中停止了 Apache，请再次启动它。

    ```bash
    sudo /usr/sbin/httpd -DFOREGROUND &
    ```

    验证 `/etc/httpd/conf/httpd.conf` 中的 `DocumentRoot` 是否设置为 `/testweb`。如果不是，请按照上一步中的操作进行修改。

    ```bash
    grep "DocumentRoot" /etc/httpd/conf/httpd.conf
    ```

    ```plaintext
    DocumentRoot "/testweb"
    ```

    此外，确认 `/testweb/index.html` 存在并具有 `root_t` 上下文。

    ```bash
    ls -Z /testweb/index.html
    ```

    ```plaintext
    system_u:object_r:root_t:s0 /testweb/index.html
    ```

2. **访问网页以确认当前行为。**

    让我们验证当前使用 `root_t` 上下文是否可以访问网页。

    ```bash
    curl http://localhost/index.html
    ```

    正如我们之前看到的，即使使用 `root_t` 上下文，也可以访问该页面。

    ```plaintext
    This is a test page.
    ```

    虽然这有效，但我们将继续演示 Web 内容的正确 SELinux 配置。

3. **为 `/testweb` 定义正确的 SELinux 文件上下文。**

    Apache 提供的 Web 内容的正确 SELinux 上下文是 `httpd_sys_content_t`。你需要使用 `semanage fcontext` 添加一个持久规则。

    ```bash
    sudo semanage fcontext -a -t httpd_sys_content_t '/testweb(/.*)?'
    ```

    此命令告诉 SELinux，`/testweb` 内的任何文件或目录（包括 `/testweb` 本身）都应标记为 `httpd_sys_content_t`。

4. **将新的 SELinux 上下文应用于文件。**

    定义规则后，你必须使用 `restorecon` 将其应用于现有文件。

    ```bash
    sudo restorecon -Rv /testweb
    ```

    你应该看到输出，表明上下文已被重新标记。

    ```plaintext
    Relabeled /testweb from system_u:object_r:root_t:s0 to system_u:object_r:httpd_sys_content_t:s0
    Relabeled /testweb/index.html from system_u:object_r:root_t:s0 to system_u:object_r:httpd_sys_content_t:s0
    ```

    再次使用 `ls -Z` 验证上下文。

    ```bash
    ls -Z /testweb/index.html
    ```

    ```plaintext
    system_u:object_r:httpd_sys_content_t:s0 /testweb/index.html
    ```

5. **再次访问网页以确认正确的配置。**

    现在，SELinux 上下文已根据最佳实践正确应用，再次尝试使用 `curl` 访问网页。

    ```bash
    curl http://localhost/index.html
    ```

    内容应该仍然可以访问，并且现在已使用推荐的 SELinux 上下文正确配置。

    ```plaintext
    This is a test page.
    ```

    这表明，虽然 `root_t` 上下文在此环境中可能有效，但使用正确的 `httpd_sys_content_t` 上下文遵循 SELinux 最佳实践，并确保跨不同安全配置的兼容性。

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
