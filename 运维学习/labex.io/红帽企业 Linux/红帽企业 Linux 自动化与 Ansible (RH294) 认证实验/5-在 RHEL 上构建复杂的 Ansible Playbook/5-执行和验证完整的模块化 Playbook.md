# 执行和验证完整的模块化 Playbook

在最后一步中，你将执行你构建的完整模块化工作流，更重要的是，你将学习如何验证自动化是否已在目标系统上达到预期状态。一个成功的 Playbook 运行固然好，但确认结果对于可靠的自动化至关重要。

首先，请确保你位于主项目目录中：

```bash
cd ~/project/ansible_patterns
```

在运行最终 Playbook 之前，让我们可视化你创建的完整项目结构。`tree` 命令非常适合此目的。如果尚未安装，你可以使用 `dnf` 进行安装：

```bash
sudo dnf install -y tree
tree .
```

你应该会看到类似以下的结构：

```plaintext
.
├── inventory
├── main.yml
├── playbooks
│   ├── db_setup.yml
│   └── web_configure.yml
└── tasks
    ├── verify_config.yml
    └── web_setup.yml

2 directories, 6 files
```

这种结构，包含一个主入口点 (`main.yml`)、单独的 Playbook 文件和可重用的任务文件，是管理 Ansible 项目的可扩展且可维护的方式。

现在，通过运行你的顶层 `main.yml` Playbook 来执行整个工作流：

```bash
ansible-playbook main.yml -i inventory
```

在 Playbook 成功完成后，下一步至关重要的是验证。你需要确认系统处于你期望的状态。我们的 Playbook 设计用于在 Web 服务器上安装 `httpd` 包，在数据库服务器上安装 `mariadb` 包。由于此实验中的所有任务都在你的本地机器上运行，我们可以使用 `rpm` 命令直接验证它们的安装。

首先，检查 `httpd` 包是否作为 Web 服务器配置的一部分被安装：

```bash
rpm -q httpd
```

你应该会看到确认包已安装的输出：

```plaintext
httpd-2.4.xx-x.el9.x86_64
```

接下来，验证数据库服务器配置中 `mariadb` 包的安装：

```bash
rpm -q mariadb
```

同样，你应该会看到 mariadb 已安装的确认信息：

```plaintext
mariadb-10.5.xx-x.el9.x86_64
```

在输出中看到包名称，即可确认你的 Ansible Playbook 已按预期成功配置了系统。你现在已成功从头到尾构建、执行和验证了一个模块化的 Ansible 项目。
