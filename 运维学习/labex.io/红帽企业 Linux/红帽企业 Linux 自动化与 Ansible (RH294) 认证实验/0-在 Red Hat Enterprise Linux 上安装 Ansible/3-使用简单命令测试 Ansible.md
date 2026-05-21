# 使用简单命令测试 Ansible

在这一步中，你将通过对本地系统运行一个简单的命令来测试你的 Ansible 安装。切换到项目目录，并使用预先配置的 inventory 文件来运行一个 Ansible ad-hoc 命令。

导航到项目目录并测试基本的 ping 功能：

```bash
cd /home/labex/project
ansible localhost -m ping
```

`ping` 模块实际上并没有发送 ICMP 数据包；相反，它验证 Ansible 是否可以连接到目标并执行 Python 代码。成功的响应将如下所示：

```plaintext
localhost | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

让我们分解一下这个输出：

- **localhost | SUCCESS**：显示命令在 localhost 目标上成功执行
- **ansible_facts**：包含在执行期间发现的系统信息
- **discovered_interpreter_python**：Ansible 找到并将使用的 Python 解释器路径
- **changed: false**：表示系统未发生任何更改（ping 是只读的）
- **ping: "pong"**：经典的响应，确认 Ansible 的连接性

“pong”响应确认 Ansible 正在正常工作，并且可以与目标系统通信。

我们还可以使用 setup 模块测试收集系统信息：

```bash
ansible localhost -m setup -a "filter=ansible_distribution*"
```

此命令使用 `setup` 模块收集系统事实，特别是过滤发行版信息。你应该看到包含有关你的 Red Hat Enterprise Linux 系统的详细信息的输出：

```plaintext
localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "RedHat",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_search_string": "Red Hat",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "9",
        "ansible_distribution_release": "Plow",
        "ansible_distribution_version": "9.6",
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false
}
```

了解系统事实输出：

- **ansible_distribution**：Linux 发行版名称 (RedHat)
- **ansible_distribution_file_parsed**：Ansible 是否成功读取发行版文件
- **ansible_distribution_file_path**：包含发行版信息的文件
- **ansible_distribution_file_search_string**：用于标识发行版的文本模式
- **ansible_distribution_file_variety**：发行版系列 (RedHat 系列)
- **ansible_distribution_major_version**：主版本号 (9)
- **ansible_distribution_release**：发布代号 (Plow)
- **ansible_distribution_version**：完整的版本号 (9.6)
- **discovered_interpreter_python**：Ansible 发现的 Python 解释器

这确认了 Ansible 可以成功地从目标主机收集系统信息，这对于基于系统特征创建条件自动化至关重要。
