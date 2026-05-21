# 使用 `file` 模块清理已管理主机上的文件

在最后这个步骤中，你将学习如何使用 `ansible.builtin.file` 模块来确保文件和目录 *不存在* 于系统上。配置管理的一个关键部分不仅在于创建和修改资源，还在于清理它们。通过将 `state` 参数设置为 `absent`，你可以指示 Ansible 删除文件、符号链接甚至整个目录。

为了完成本次实验，我们将编写一个单一的“清理”playbook，它将删除我们在前几个步骤中创建的所有文件：`/tmp/info.txt`、`/etc/motd`、`/etc/issue` 以及 `/etc/issue.net` 符号链接。

1. **首先，确保你在 `~/project` 目录中。**

    ```bash
    cd ~/project
    ```

2. **创建一个名为 `cleanup.yml` 的新 playbook**。此 playbook 将包含恢复我们更改所需的所有任务。

    ```bash
    nano ~/project/cleanup.yml
    ```

3. **将以下内容添加到你的 `cleanup.yml` playbook 中。** 此 playbook 使用任务列表，每个任务都针对我们创建的文件之一。请注意，`become: true` 是在 play 级别设置的，因此所有任务都将以提升的权限运行。

    ```yaml
    ---
    - name: Clean up managed files from the system
      hosts: localhost
      become: true
      tasks:
        - name: Remove the temporary info file
          ansible.builtin.file:
            path: /tmp/info.txt
            state: absent

        - name: Remove the custom MOTD file
          ansible.builtin.file:
            path: /etc/motd
            state: absent

        - name: Remove the custom issue file
          ansible.builtin.file:
            path: /etc/issue
            state: absent

        - name: Remove the issue.net symbolic link
          ansible.builtin.file:
            path: /etc/issue.net
            state: absent
    ```

    此 playbook 的关键在于每个任务中的 `state: absent` 参数。这告诉 `file` 模块确保指定 `path` 的项不存在。如果找到文件，它将删除它。如果文件已不存在，它将不做任何操作，从而保持幂等性。

4. **执行清理 playbook。**

    ```bash
    ansible-playbook -i inventory.ini cleanup.yml
    ```

    输出将显示每个任务通过删除文件成功进行了更改。

    ```plaintext
    PLAY [Clean up managed files from the system] **********************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Remove the temporary info file] ******************************************
    changed: [localhost]

    TASK [Remove the custom MOTD file] *********************************************
    changed: [localhost]

    TASK [Remove the custom issue file] ********************************************
    changed: [localhost]

    TASK [Remove the issue.net symbolic link] **************************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

5. **验证文件是否已被删除。** 你可以使用 `ls` 命令检查它们是否存在。该命令将报告无法访问它们，因为它们已不存在。

    ```bash
    ls /tmp/info.txt /etc/motd /etc/issue /etc/issue.net
    ```

    预期的输出是一系列错误，这证实了清理工作已成功完成。

    ```plaintext
    ls: cannot access '/tmp/info.txt': No such file or directory
    ls: cannot access '/etc/motd': No such file or directory
    ls: cannot access '/etc/issue': No such file or directory
    ls: cannot access '/etc/issue.net': No such file or directory
    ```

你现在已经成功使用 Ansible 删除了文件并清理了系统，完成了从创建到删除的文件管理全生命周期。
