# Apache Web 服务器 SELinux 拒绝访问故障排除

在这一步中，你将学习如何识别和排除 SELinux 安全拒绝，特别关注可能阻止 Apache Web 服务器正常运行的问题。当 SELinux 阻止一个操作时，它会记录一条“访问向量缓存”（AVC）拒绝消息。这些消息对于理解操作失败的原因以及如何解决它至关重要。

你将使用 `auditd`（Linux 审计系统守护程序）和 `sealert` 等工具来分析这些拒绝消息。`auditd` 收集系统调用和事件，包括 SELinux 拒绝，并将它们存储在 `/var/log/audit/audit.log` 中。`sealert` 工具是 `setroubleshoot-server` 包的一部分，可以解析这些日志，并为 SELinux 拒绝提供人类可读的解释和建议的解决方案。

首先，你需要确保安装了 `auditd` 和 `setroubleshoot-server`。

1. **安装 `auditd` 和 `setroubleshoot-server`。**

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

    现在，创建一个新目录并在其中创建一个 `index.html` 文件。这次，你将故意为该文件设置不正确的 SELinux 上下文以触发拒绝。

    ```bash
    sudo mkdir /testweb
    echo 'This is a test page.' | sudo tee /testweb/index.html
    ```

    默认情况下，`/testweb/index.html` 可能会具有 `root_t` 上下文。让我们确认一下。

    ```bash
    ls -Z /testweb/index.html
    ```

    ```plaintext
    system_u:object_r:root_t:s0 /testweb/index.html
    ```

    现在，让我们配置 Apache 以从 `/testweb` 提供服务。打开 `/etc/httpd/conf/httpd.conf`。

    ```bash
    sudo nano /etc/httpd/conf/httpd.conf
    ```

    将 `DocumentRoot` 和 `<Directory>` 指令更改为 `/testweb`。

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

    尝试使用 `curl` 访问网页。

    ```bash
    curl http://localhost/index.html
    ```

    **重要提示**：在此环境中，你可能会发现即使使用 `root_t` 上下文，网页也可以访问，这与我们在步骤 2 中观察到的类似。这表明，虽然 SELinux 正在执行，但 `root_t` 上下文具有比更严格的上下文更广泛的权限。

    ```plaintext
    This is a test page.
    ```

    但是，为了学习 SELinux 故障排除技术，我们将继续进行，就好像存在拒绝一样。在更严格的 SELinux 环境或不同的策略配置下，访问具有不适当上下文的文件确实会生成拒绝。

4. **了解如何使用 `ausearch` 调查 SELinux 拒绝。**

    `ausearch` 命令用于查询审计日志。你可以搜索今天发生的 SELinux AVC 拒绝（`-m AVC`）（`-ts today`）。

    ```bash
    sudo ausearch -m AVC -ts today
    ```

    **注意**：由于在我们的环境中可以访问网页，因此你可能不会看到与此特定测试相关的任何最近的 AVC 拒绝。但是，如果存在拒绝，此命令通常会输出详细的审计日志条目。在典型的拒绝场景中，你将查找与 `httpd` 和 `/testweb/index.html` 相关的条目。

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
    - `tcontext=system_u:object_r:root_t:s0`：目标的 SELinux 上下文（你的 `index.html` 文件）。
    - `tclass=file`：目标的类型（文件）。

    此输出清楚地表明，`httpd_t`（Apache）被拒绝了对具有 `default_t` 上下文的文件的 `getattr` 访问。

5. **了解如何使用 `sealert` 进行 SELinux 分析。**

    `sealert` 可以解析审计日志并提供更友好的信息。你可以运行 `sealert -a` 来分析所有最近的拒绝，或者如果你有来自 `/var/log/messages` 中的 `setroubleshoot` 消息的特定 UUID，则可以使用 `sealert -l <UUID>`。

    ```bash
    sudo sealert -a /var/log/audit/audit.log
    ```

    **注意**：由于我们在此环境中尚未遇到实际拒绝，因此运行 `sealert` 可能不会显示与我们的 `/testweb` 示例相关的结果。但是，在发生 SELinux 拒绝的情况下，`sealert` 将分析审计日志并提供摘要。

    `sealert` 的典型输出，用于 httpd 上下文问题，将如下所示：

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

    在实际拒绝场景中，`sealert` 输出将非常有帮助。它将明确说明问题并提出解决方案，例如使用 `semanage fcontext -a -t FILE_TYPE '/testweb/index.html'` 更改标签，并将 `httpd_sys_content_t` 列为合适的 `FILE_TYPE`。

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
