# 为 Web 服务器创建静态 Inventory 文件

在本步骤中，你将学习 Ansible inventory 的基础知识。Inventory 是一个文本文件，列出了 Ansible 将要管理的服务器（或“托管节点”）。你将为一组 Web 服务器创建一个简单的静态 inventory 文件，并学习如何验证其内容。

首先，你需要确保你的系统已安装 Ansible。由于它不是默认安装的，你将使用 `dnf` 包管理器来安装它。

1. 打开你的终端并安装 `ansible-core` 包，它提供了 Ansible 的基础命令行工具。

    ```bash
    sudo dnf install -y ansible-core
    ```

    你应该会看到输出表明该软件包正在安装和验证。

    ```plaintext
    ...
    Installed:
      ansible-core-2.16.x-x.el9.x86_64
    ...
    Complete!
    ```

2. 为了更好地组织，请在你的主目录下为这个项目创建一个专用目录。我们将其命名为 `ansible-lab`。

    ```bash
    mkdir -p ~/project/ansible-lab
    ```

3. 进入你新创建的项目目录。本实验中所有后续操作都将在此位置进行。

    ```bash
    cd ~/project/ansible-lab
    ```

4. 现在，你将创建你的第一个 inventory 文件。Inventory 文件通常以类似 INI 的格式编写。你将使用 `nano` 文本编辑器创建一个名为 `inventory` 的文件。

    ```bash
    # install nano
    sudo dnf install -y nano
    ```

    ```bash
    nano inventory
    ```

5. 在 `nano` 编辑器中，添加以下内容。此配置定义了一个名为 `[webservers]` 的组，并将你的本地机器 `localhost` 添加到该组中。

    - `[webservers]` 是一个组名。组用于通过单个命令定位多个主机。
    - `localhost` 是你要管理的机器的主机名。在这种情况下，它是 LabEx 虚拟机本身。
    - `ansible_connection=local` 是一个特殊变量，它告诉 Ansible 直接在控制节点（你的虚拟机）上执行命令，而不是尝试通过 SSH 连接到它。

    ```ini
    [webservers]
    localhost ansible_connection=local
    ```

    要在 `nano` 中保存文件，请按 `Ctrl+O`，然后按 `Enter` 确认文件名，最后按 `Ctrl+X` 退出编辑器。

6. 创建了 inventory 文件后，你可以使用 `ansible-inventory` 命令来解析该文件并显示其中包含的主机列表。`-i` 标志指定了 inventory 文件的路径。

    ```bash
    ansible-inventory --list -i inventory
    ```

    该命令将输出 inventory 的 JSON 格式表示，这证实了 Ansible 可以正确读取和理解你的文件。

    ```json
    {
      "_meta": {
        "hostvars": {
          "localhost": {
            "ansible_connection": "local"
          }
        }
      },
      "all": {
        "children": ["ungrouped", "webservers"]
      },
      "webservers": {
        "hosts": ["localhost"]
      }
    }
    ```

你已成功创建了一个基本的静态 inventory 文件，并验证了 Ansible 可以正确解析它。此 inventory 文件将是你后续步骤中编写 playbook 的基础。
