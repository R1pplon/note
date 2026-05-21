# 准备 RHEL 环境并配置 Ansible 日志记录

在此步骤中，你将为 Ansible 自动化准备你的 Red Hat Enterprise Linux 环境。这包括安装必要的软件、创建一个专用的项目目录以及设置一个基本配置文件来控制 Ansible 的行为并启用日志记录。正确的设置是有效自动化和故障排除的第一步。

1. **安装 Ansible**

    首先，你需要安装 Ansible。核心自动化引擎由 `ansible-core` 包提供。使用 `dnf` 包管理器和 `sudo` 进行安装。`-y` 标志会自动对任何确认提示回答“是”。

    ```bash
    sudo dnf install -y ansible-core
    ```

    你应该会看到输出，表明该软件包及其依赖项正在安装。

    ```plaintext
    Last metadata expiration check: ...
    Dependencies resolved.
    ================================================================================
     Package             Architecture   Version                Repository      Size
    ================================================================================
    Installing:
     ansible-core        x86_64         <version>              <repo>          2.8 M
    ...
    Transaction Summary
    ================================================================================
    Install  XX Packages

    Total download size: XX M
    Installed size: XX M
    ...
    Complete!
    ```

2. **创建项目目录**

    将 Ansible 项目组织在专用目录中是一种最佳实践。这可以使你的 playbook、清单（inventory）和配置文件整洁地分开。让我们在你的主项目文件夹中创建一个名为 `ansible_troubleshooting` 的目录，并进入该目录。

    ```bash
    mkdir -p ~/project/ansible_troubleshooting
    cd ~/project/ansible_troubleshooting
    ```

    从现在开始，本实验中的所有命令都将在 `~/project/ansible_troubleshooting` 目录内执行。

3. **创建 Ansible 清单文件**

    清单（inventory）是一个列出 Ansible 将要管理的宿主机（或节点）的文件。由于你正在单台 LabEx VM 上工作，你将配置 Ansible 来管理本地机器本身。

    创建一个名为 `inventory` 的文件，并在其中添加 `localhost`。`ansible_connection=local` 部分告诉 Ansible 直接在控制节点（你的 VM）上执行命令，而无需使用 SSH。

    ```bash
    echo "localhost ansible_connection=local" > inventory
    ```

    你可以使用 `cat` 命令验证文件内容：

    ```bash
    cat inventory
    ```

    **预期输出：**

    ```plaintext
    localhost ansible_connection=local
    ```

4. **配置 Ansible 日志**

    `ansible.cfg` 文件允许你为特定项目自定义 Ansible 的行为。当放置在项目目录中时，其设置将覆盖系统范围的默认设置。在这里，你将创建此文件来指定你的清单位置并启用日志记录。日志记录对于故障排除至关重要，因为它会记录每次 playbook 运行的详细信息。

    使用 `nano` 编辑器创建 `ansible.cfg` 文件。

    ```bash
    nano ansible.cfg
    ```

    现在，将以下内容复制并粘贴到 `nano` 编辑器中。此配置告诉 Ansible 使用当前目录中的 `inventory` 文件，并将所有日志输出写入名为 `ansible.log` 的文件。

    ```ini
    [defaults]
    inventory = /home/labex/project/ansible_troubleshooting/inventory
    log_path = /home/labex/project/ansible_troubleshooting/ansible.log
    ```

    要在 `nano` 中保存文件，请按 `Ctrl+X`，然后按 `Y` 确认，最后按 `Enter` 写入文件。

    你的环境现在已完全准备就绪。你已安装 Ansible，并配置了一个包含本地清单和已启用日志记录的项目目录，为接下来的步骤做好了准备。
