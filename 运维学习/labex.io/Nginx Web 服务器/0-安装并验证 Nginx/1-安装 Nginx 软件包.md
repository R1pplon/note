# 安装 Nginx 软件包

在这一步中，你将使用系统的软件包管理器安装 Nginx 软件。

Ubuntu 使用名为 `apt`（Advanced Package Tool）的工具。你可以将 `apt` 理解为 Linux 命令行下的「应用商店」——它负责为你下载并安装软件。

要安装 Nginx，请在终端中运行以下命令。我们使用 `sudo`（意为「SuperUser DO」）以管理员权限运行该命令。

与其他基础设施软件包一样，安装 Nginx 会将程序和配置文件放入磁盘。运行服务则是下一步要处理的问题。

```bash
sudo apt install nginx -y
```

**该命令的作用是什么？**

* `sudo`：授予安装所需的管理员权限。
* `apt install`：告诉系统安装一个软件包。
* `nginx`：我们要安装的软件包名称。
* `-y`：自动回答确认提示中的「yes」，使安装过程更顺畅。

安装完成后，通过检查版本来验证 Nginx 是否已安装：

```bash
nginx -v
```

你应该会看到类似 `nginx version: nginx/1.18.0...` 的输出。这确认了软件已安装在你的系统中。
