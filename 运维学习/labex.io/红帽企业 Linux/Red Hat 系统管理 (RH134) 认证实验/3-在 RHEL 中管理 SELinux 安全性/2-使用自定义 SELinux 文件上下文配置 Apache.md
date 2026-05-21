# 使用自定义 SELinux 文件上下文配置 Apache

在这一步中，你将学习如何配置 Apache，以便从非标准目录提供 Web 内容并管理其 SELinux 文件上下文。默认情况下，SELinux 策略限制 Apache (httpd) 仅从特定目录（主要是 `/var/www/html`）提供文件。如果你将 Web 内容放置在不同的位置，即使文件系统权限正确，SELinux 也会阻止 Apache 访问它。这就是 SELinux 文件上下文发挥作用的地方。

SELinux 文件上下文是应用于文件或目录的标签，用于定义其安全属性。为了让 Apache 从自定义位置提供内容，该位置及其内容必须具有正确的 SELinux 上下文，通常是 `httpd_sys_content_t`。你将使用 `semanage fcontext` 定义一个持久规则，并使用 `restorecon` 应用它。

首先，你需要安装 Apache HTTP 服务器。

1. **安装 Apache HTTP 服务器。**

    使用 `dnf` 包管理器安装 `httpd` 包。

    ```bash
    sudo dnf install -y httpd
    ```

    你应该看到输出，表明成功安装了 `httpd` 包及其依赖项。

2. **为 Web 内容创建一个自定义目录和一个 `index.html` 文件。**

    你将创建一个名为 `/custom` 的新目录，并在其中放置一个简单的 `index.html` 文件。这将是你的非标准 Web 文档根目录。

    ```bash
    sudo mkdir /custom
    echo 'This is custom web content.' | sudo tee /custom/index.html
    ```

    验证 `index.html` 文件的内容。

    ```bash
    cat /custom/index.html
    ```

    ```plaintext
    This is custom web content.
    ```

3. **配置 Apache 以使用新的文档根目录。**

    Apache 的主配置文件是 `/etc/httpd/conf/httpd.conf`。你需要编辑此文件，将 Apache 指向你的新 `/custom` 目录，而不是默认的 `/var/www/html`。

    使用 `nano` 打开配置文件。

    ```bash
    sudo nano /etc/httpd/conf/httpd.conf
    ```

    在编辑器中，找到行 `DocumentRoot "/var/www/html"` 和 `<Directory "/var/www/html">`。将所有出现的 `/var/www/html` 更改为 `/custom`。

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

    修改配置后，你需要启动 `httpd` 服务。由于你在容器环境中，`systemctl` 不可用。你将直接启动 `httpd`。

    ```bash
    sudo /usr/sbin/httpd -DFOREGROUND &
    ```

    `&` 符号在后台运行命令，允许你继续使用终端。你应该看到类似这样的输出，表明 Apache 正在启动。

    ```plaintext
    [1] 5094
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using fe80::216:3eff:fe00:63b%eth0. Set the 'ServerName' directive globally to suppress this message
    ```

    **注意**：关于服务器完全限定域名（FQDN）的警告消息在此实验环境中是正常的，可以安全地忽略。

    要验证 Apache 是否正在运行，你可以检查 `httpd` 进程。

    ```bash
    ps aux | grep httpd
    ```

    你应该看到几个 `httpd` 进程正在运行。

    ```plaintext
    root        ... /usr/sbin/httpd -DFOREGROUND
    apache      ... /usr/sbin/httpd -DFOREGROUND
    ...output omitted...
    ```

5. **尝试访问网页。**

    现在，尝试使用 `curl` 访问你的网页。由于你在同一台机器上，你可以使用 `localhost`。

    ```bash
    curl http://localhost/index.html
    ```

    **注意**：在此特定环境中，你可能会发现即使使用 `root_t` 上下文，网页也可以访问。这表明虽然 SELinux 正在执行，但 `root_t` 上下文可能具有比预期更广泛的权限。但是，为了安全最佳实践和正确的 SELinux 配置，Web 内容仍应使用适当的 `httpd_sys_content_t` 上下文。

    ```plaintext
    This is custom web content.
    ```

6. **检查自定义目录的当前 SELinux 上下文。**

    使用 `ls -Z` 命令查看你的 `/custom` 目录和 `index.html` 文件的 SELinux 上下文。

    ```bash
    ls -Zd /custom /custom/index.html
    ```

    你会注意到它们具有 `root_t` 上下文，这不是 Apache Web 内容的推荐上下文。

    ```plaintext
    system_u:object_r:root_t:s0 /custom
    system_u:object_r:root_t:s0 /custom/index.html
    ```

    将其与默认的 Apache 文档根目录进行比较：

    ```bash
    ls -Zd /var/www/html
    ```

    你将看到 `/var/www/html` 具有 `httpd_sys_content_t` 上下文。这是你需要应用于你的自定义目录的上下文。

    ```plaintext
    system_u:object_r:httpd_sys_content_t:s0 /var/www/html
    ```

7. **为 `/custom` 定义一个持久的 SELinux 文件上下文规则。**

    `semanage fcontext` 命令用于管理 SELinux 文件上下文映射规则。`-a` 选项添加一个新规则，`-t` 指定目标类型，正则表达式 `'/custom(/.*)?'` 匹配 `/custom` 目录本身以及其中的所有文件和子目录。

    ```bash
    sudo semanage fcontext -a -t httpd_sys_content_t '/custom(/.*)?'
    ```

    此命令将规则添加到 SELinux 策略中，但它不会立即更改现有文件的上下文。

8. **将新的 SELinux 上下文应用于文件。**

    `restorecon` 命令用于将文件和目录的 SELinux 上下文恢复为其默认值，这些默认值由策略定义。`-R` 选项递归地应用更改，`-v` 提供详细输出。

    ```bash
    sudo restorecon -Rv /custom
    ```

    你应该看到输出，表明 `/custom` 和 `/custom/index.html` 的上下文已被重新标记。

    ```plaintext
    Relabeled /custom from system_u:object_r:root_t:s0 to system_u:object_r:httpd_sys_content_t:s0
    Relabeled /custom/index.html from system_u:object_r:root_t:s0 to system_u:object_r:httpd_sys_content_t:s0
    ```

    再次使用 `ls -Z` 验证上下文。

    ```bash
    ls -Zd /custom /custom/index.html
    ```

    它们现在应该具有 `httpd_sys_content_t` 上下文。

    ```plaintext
    system_u:object_r:httpd_sys_content_t:s0 /custom
    system_u:object_r:httpd_sys_content_t:s0 /custom/index.html
    ```

9. **再次访问网页。**

    现在 SELinux 上下文已正确，再次尝试使用 `curl` 访问网页。

    ```bash
    curl http://localhost/index.html
    ```

    你现在应该看到你的 `index.html` 文件的内容。

    ```plaintext
    This is custom web content.
    ```

    最后，停止 Apache HTTP 服务器进程。

    ```bash
    sudo pkill httpd
    ```

    验证没有 `httpd` 进程正在运行。

    ```bash
    ps aux | grep httpd
    ```

    你应该只看到 `grep` 进程本身。

    ```plaintext
    labex     ... grep httpd
    ```
