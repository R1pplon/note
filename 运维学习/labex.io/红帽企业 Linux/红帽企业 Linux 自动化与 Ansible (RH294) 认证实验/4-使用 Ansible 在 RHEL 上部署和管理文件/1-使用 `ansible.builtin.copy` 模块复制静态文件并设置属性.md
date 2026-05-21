# 使用 `ansible.builtin.copy` 模块复制静态文件并设置属性

在本步骤中，你将学习如何使用最基础的 Ansible 模块之一：`ansible.builtin.copy`。此模块用于将文件从你的控制节点（LabEx VM）传输到托管主机上的指定位置。在我们的例子中，托管主机将是 `localhost` 本身。除了复制文件，`copy` 模块还允许你精确控制文件的属性，例如其所有者、组和权限模式，这对于正确的系统配置至关重要。

首先，让我们设置项目环境。我们所有的工作都将在 `~/project` 目录下进行。

1. **导航到项目目录并为源文件创建一个子目录。** 这是一个保持项目组织的常见做法。

    安装 `ansible-core` 包。

    ```bash
    sudo dnf install -y ansible-core
    ```

    然后，导航到项目目录并为源文件创建一个子目录。

    ```bash
    cd ~/project
    mkdir files
    ```

2. **接下来，创建一个简单的文本文件供我们复制。** 我们将使用 `cat` 命令配合“here document”在 `files` 目录中创建 `info.txt` 文件。

    ```bash
    cat << EOF > ~/project/files/info.txt
    This file was deployed by Ansible.
    It contains important system information.
    EOF
    ```

3. **现在，创建一个 Ansible inventory 文件。** inventory 文件告诉 Ansible 要管理哪些主机。对于本次实验，我们将管理本地机器。创建一个名为 `inventory.ini` 的文件。

    ```bash
    cat << EOF > ~/project/inventory.ini
    localhost ansible_connection=local
    EOF
    ```

    在此 inventory 中，`localhost` 是我们要定位的主机。变量 `ansible_connection=local` 指示 Ansible 直接在控制节点上执行任务，而无需使用 SSH。

4. **创建你的第一个 Ansible playbook。** 这个 playbook 将包含复制文件的指令。使用 `nano` 或 `cat` 创建一个名为 `copy_file.yml` 的文件。

    ```bash
    nano ~/project/copy_file.yml
    ```

    将以下内容添加到文件中。这个 playbook 定义了一个任务：将 `info.txt` 复制到 `/tmp/` 目录并设置其属性。

    ```yaml
    ---
    - name: Deploy a static file to localhost
      hosts: localhost
      tasks:
        - name: Copy info.txt and set attributes
          ansible.builtin.copy:
            src: files/info.txt
            dest: /tmp/info.txt
            owner: labex
            group: labex
            mode: "0640"
    ```

    让我们分解一下 `copy` 任务中的参数：

    - `src: files/info.txt`: 源文件在控制节点上的路径，相对于 playbook 的位置。
    - `dest: /tmp/info.txt`: 文件将在托管主机上放置的绝对路径。
    - `owner: labex`: 将文件的所有者设置为 `labex` 用户。
    - `group: labex`: 将文件的组设置为 `labex` 组。
    - `mode: '0640'`: 设置文件的权限。`0640` 表示所有者可读/写，组可读，其他人无权限。

5. **使用 `ansible-playbook` 命令执行 playbook。** `-i` 标志指定了我们的 inventory 文件。

    ```bash
    ansible-playbook -i inventory.ini copy_file.yml
    ```

    你应该会看到类似以下的 playbook 执行成功的输出：

    ```plaintext
    PLAY [Deploy a static file to localhost] ***************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Copy info.txt and set attributes] ****************************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

6. **最后，验证文件是否已正确复制** 并具有正确的属性。使用 `ls -l` 命令检查权限、所有者和组。

    ```bash
    ls -l /tmp/info.txt
    ```

    输出应显示 `labex` 是所有者和组，权限为 `-rw-r-----`。

    ```plaintext
    -rw-r----- 1 labex labex 72 Jul 10 14:30 /tmp/info.txt
    ```

    你还可以查看文件的内容，以确保它已完整复制。

    ```bash
    cat /tmp/info.txt
    ```

    ```plaintext
    This file was deployed by Ansible.
    It contains important system information.
    ```

你已成功使用 `ansible.builtin.copy` 模块在本地系统上部署了文件并配置了其属性。
