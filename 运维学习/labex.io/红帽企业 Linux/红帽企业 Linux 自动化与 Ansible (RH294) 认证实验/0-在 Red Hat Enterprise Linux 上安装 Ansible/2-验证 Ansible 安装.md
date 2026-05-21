# 验证 Ansible 安装

现在你已经安装了 Ansible Core，让我们通过检查版本并确认基本的命令行工具是否可用，来验证安装是否成功。

首先，通过运行以下命令来检查 Ansible 的版本：

```bash
ansible --version
```

此命令显示有关你的 Ansible 安装的详细信息，包括核心版本、Python 版本以及各种组件的位置。你应该看到类似这样的输出：

```plaintext
ansible [core 2.14.18]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/labex/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3.9/site-packages/ansible
  ansible collection location = /home/labex/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.9.21 (main, Feb 10 2025, 00:00:00) [GCC 11.5.0 20240719 (Red Hat 11.5.0-5)] (/usr/bin/python3)
  jinja version = 3.1.2
  libyaml = True
```

让我们了解每一行的含义：

- **ansible [core 2.14.18]**：显示已安装的 Ansible Core 版本
- **config file**：指向包含默认设置的主 Ansible 配置文件
- **configured module search path**：Ansible 查找自定义模块的目录
- **ansible python module location**：安装核心 Ansible Python 代码的位置
- **ansible collection location**：存储 Ansible 集合（打包的模块和插件）的目录
- **executable location**：ansible 命令二进制文件的实际位置
- **python version**：Ansible 使用的 Python 解释器版本
- **jinja version**：Ansible 用于动态内容的模板引擎版本
- **libyaml = True**：确认快速 YAML 解析器可用于提高性能

这确认了 Ansible 已正确安装并可以使用。接下来，我们还要检查 `ansible-playbook` 命令是否可用：

```bash
ansible-playbook --version
```

你应该看到类似 ansible-playbook 工具的版本信息，该工具对于运行 Ansible playbook 至关重要。
