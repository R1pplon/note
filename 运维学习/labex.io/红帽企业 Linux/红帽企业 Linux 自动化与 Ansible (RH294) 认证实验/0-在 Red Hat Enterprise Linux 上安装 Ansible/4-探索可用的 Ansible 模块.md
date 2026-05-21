# 探索可用的 Ansible 模块

Ansible 附带数百个内置模块，用于各种自动化任务。让我们探索一些可用的模块，以了解安装后立即可用的功能。

要查看可用模块的列表，请运行：

```bash
ansible-doc -l | head -20
```

`ansible-doc -l` 命令列出所有可用的模块，使用 `head -20` 显示前 20 个模块。这让你了解 Ansible 提供的广泛的自动化功能。你将看到类似这样的输出：

```plaintext
ansible.builtin.add_host               Add a host (and alternatively a grou...
ansible.builtin.apt                    Manages apt-packages
ansible.builtin.apt_key                Add or remove an apt key
ansible.builtin.apt_repository         Add and remove APT repositories
ansible.builtin.assemble               Assemble configuration files from fr...
ansible.builtin.assert                 Asserts given expressions are true
ansible.builtin.async_status           Obtain status of asynchronous task
ansible.builtin.blockinfile            Insert/update/remove a text block su...
ansible.builtin.command                Execute commands on targets
ansible.builtin.copy                   Copy files to remote locations
ansible.builtin.cron                   Manage cron.d and crontab entries
ansible.builtin.debconf                Configure a .deb package
ansible.builtin.debug                  Print statements during execution
ansible.builtin.dnf                    Manages packages with the `dnf' pack...
ansible.builtin.dpkg_selections        Dpkg package selection selections
ansible.builtin.expect                 Executes a command and responds to p...
ansible.builtin.fail                   Fail with custom message
ansible.builtin.fetch                  Fetch files from remote nodes
ansible.builtin.file                   Manage files and file properties
ansible.builtin.find                   Return a list of files based on spec...
```

了解模块列表格式：

- **ansible.builtin.**：表示这些是 Ansible Core 附带的内置模块
- **Module name**：在 playbook 或 ad-hoc 命令中调用模块时使用的名称
- **Description**：对模块功能的简要说明

你通常会用到的一些重要模块：

- **command**：在目标系统上执行 shell 命令
- **copy**：将文件从你的控制机复制到远程主机
- **dnf**：在 Red Hat 系统上安装、更新或删除软件包
- **file**：创建目录、设置权限或管理文件属性
- **debug**：在 playbook 执行期间打印消息以进行故障排除

要获取特定模块的详细文档，你可以将 ansible-doc 命令与模块名称一起使用。例如，要了解 copy 模块：

```bash
ansible-doc copy
```

这将显示 `copy` 模块的全面文档，包括示例和参数说明。 `ansible-doc` 命令为任何 Ansible 模块提供详细的文档，让你轻松学习如何使用不同的自动化功能。阅读完毕后，按 `q` 退出文档查看器。
