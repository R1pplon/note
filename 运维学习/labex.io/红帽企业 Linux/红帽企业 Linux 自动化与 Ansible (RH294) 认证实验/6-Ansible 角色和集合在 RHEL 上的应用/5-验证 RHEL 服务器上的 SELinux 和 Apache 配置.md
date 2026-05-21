# 验证 RHEL 服务器上的 SELinux 和 Apache 配置

在最后一步中，你将验证你的 Ansible 自动化是否已正确配置系统。确认服务是否按预期运行以及安全策略（SELinux）是否已正确应用至关重要。你将使用标准的 RHEL 命令行工具来检查系统状态。

首先，请确保你位于主项目目录中。

```bash
cd ~/project
```

### 1. 验证 SELinux 配置

来自 collections 的 SELinux 模块的任务是将 SELinux 模式设置为 `enforcing`，并为 `http_port_t` 类型允许新端口。

使用 `sestatus` 命令检查当前的 SELinux 状态。

```bash
sestatus
```

输出应显示 SELinux 已启用并处于 `enforcing` 模式。

```plaintext
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      33
```

接下来，使用 `semanage port` 命令验证端口 `9081` 和 `9082` 是否已添加到 `http_port_t` 上下文中。你可以将输出通过管道传递给 `grep` 来查找相关行。

```bash
sudo semanage port -l | grep http_port_t
```

你应该会在默认 HTTP 端口列表中看到你自定义的端口。确切的输出可能会有所不同，但它将包含你定义的端口。

```plaintext
http_port_t                    tcp      9082, 9081, 80, 81, 443, 488, 8008, 8009, 8443, 9000
pegasus_http_port_t            tcp      5988
```

这证实了 SELinux 模块已成功更新策略。

### 2. 验证 Apache 服务和配置

`infra.apache` 角色安装并启动了 `httpd` 服务。由于此容器环境中没有 `systemctl`，你可以使用 `ps` 来检查正在运行的进程。

```bash
ps aux | grep httpd
```

你应该会看到多个 `httpd` 进程正在运行，这表明服务已激活。

```plaintext
root        8851  0.2  0.4  25652 16228 ?        Ss   09:31   0:00 /usr/sbin/httpd -DFOREGROUND
apache      8852  0.0  0.1  25308  6044 ?        S    09:31   0:00 /usr/sbin/httpd -DFOREGROUND
apache      8853  0.0  0.3 1443348 11364 ?       Sl   09:31   0:00 /usr/sbin/httpd -DFOREGROUND
apache      8854  0.0  0.3 1443348 11480 ?       Sl   09:31   0:00 /usr/sbin/httpd -DFOREGROUND
apache      8855  0.0  0.4 1574484 15848 ?       Sl   09:31   0:00 /usr/sbin/httpd -DFOREGROUND
labex       9298  0.0  0.0   6408  2176 pts/3    S+   09:31   0:00 grep --color=auto httpd
```

### 3. 验证 Web 内容的可访问性

最后，最重要的测试是查看开发者网站是否可访问。你的 `apache.developer_configs` 角色在端口 `9081` 和 `9082` 上设置了虚拟主机。使用 `curl` 命令从每个端点请求内容。

首先，测试用户 `jdoe` 在端口 `9081` 上的网站。

```bash
curl http://localhost:9081
```

预期的输出是你为该用户创建的 `index.html` 文件的内容。

```plaintext
Welcome to jdoe's dev space
```

接下来，测试用户 `jdoe2` 在端口 `9082` 上的网站。

```bash
curl http://localhost:9082
```

你应该会看到相应的欢迎消息。

```plaintext
Welcome to jdoe2's dev space
```

这些成功的 `curl` 命令证实了 Apache 已正确配置，虚拟主机正在运行，并且 SELinux 策略允许通过自定义端口进行流量。

恭喜！你已成功构建了一个完整的 Ansible 自动化项目，该项目结合了自定义角色、来自 Git 存储库的角色以及来自 Ansible collections 的 SELinux 模块，以配置一个安全的多租户开发 Web 服务器。
