# 使用 `copy` 和 `file` 模块部署支持文件并创建符号链接

在本步骤中，你将结合 `copy` 模块的知识和一个新的、功能强大的模块：`ansible.builtin.file`。`copy` 用于传输内容，而 `file` 则用于管理托管主机上文件、目录和符号链接的状态。你将使用它来创建目录、设置权限，以及（在本实验中最重要的）创建符号链接。

我们的场景是配置系统显示的登录前消息。在许多 Linux 系统中，`/etc/issue` 会显示给本地终端用户，而 `/etc/issue.net` 会显示给远程用户（例如通过 SSH）。我们将部署一个单独的 `issue` 文件，然后创建一个符号链接，使 `/etc/issue.net` 指向 `/etc/issue`，从而确保它们始终显示相同的消息。

1. **首先，确保你在 `~/project` 目录中，并为你的 issue 消息创建源文件。** 我们将把这个文件放在你之前创建的 `files` 子目录中。

    ```bash
    cd ~/project
    cat << EOF > ~/project/files/issue
    Authorized access only.
    All connections are logged and monitored.
    EOF
    ```

2. **创建一个名为 `deploy_issue.yml` 的新 playbook**。此 playbook 将包含两个任务：一个用于复制 `issue` 文件，另一个用于创建符号链接。

    ```bash
    nano ~/project/deploy_issue.yml
    ```

3. **将以下内容添加到你的 `deploy_issue.yml` playbook 中。** 此 playbook 需要提升的权限 (`become: true`) 才能管理 `/etc/` 目录中的文件。

    ```yaml
    ---
    - name: Configure system issue files
      hosts: localhost
      become: true
      tasks:
        - name: Copy custom /etc/issue file
          ansible.builtin.copy:
            src: files/issue
            dest: /etc/issue
            owner: root
            group: root
            mode: "0644"

        - name: Ensure /etc/issue.net is a symlink to /etc/issue
          ansible.builtin.file:
            src: /etc/issue
            dest: /etc/issue.net
            state: link
            force: yes
    ```

    让我们分析新的 `ansible.builtin.file` 任务：

    - `src: /etc/issue`: 当 `state` 为 `link` 时，`src` 指定符号链接应指向的文件。
    - `dest: /etc/issue.net`: 这是创建符号链接本身的目标路径。
    - `state: link`: 这个关键参数告诉 `file` 模块创建符号链接，而不是普通文件或目录。
    - `force: yes`: 这是一个有用的选项，可以确保幂等性。如果 `/etc/issue.net` 已作为普通文件存在，Ansible 将删除它并创建链接。如果没有 `force: yes`，在这种情况下 playbook 将会失败。

4. **执行 playbook。**

    ```bash
    ansible-playbook -i inventory.ini deploy_issue.yml
    ```

    输出将显示两个任务都成功地进行了更改。

    ```plaintext
    PLAY [Configure system issue files] ********************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Copy custom /etc/issue file] *********************************************
    changed: [localhost]

    TASK [Ensure /etc/issue.net is a symlink to /etc/issue] ************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

5. **使用 `ls -l` 命令验证结果。** 此命令提供详细列表，清晰地显示符号链接。

    ```bash
    ls -l /etc/issue /etc/issue.net
    ```

    输出应显示 `/etc/issue` 是一个普通文件，而 `/etc/issue.net` 是一个指向它的符号链接。`/etc/issue.net` 权限开头的 `l` 表示它是一个链接。

    ```plaintext
    -rw-r--r--. 1 root root 65 Jul 10 15:00 /etc/issue
    lrwxrwxrwx. 1 root root 10 Jul 10 15:00 /etc/issue.net -> /etc/issue
    ```

你现在已经成功部署了一个配置文件，并使用 `ansible.builtin.file` 模块创建了一个符号链接，这是管理系统配置的一种常见且强大的模式。
