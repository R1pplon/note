# 编写 Playbook 来安装和启动 Apache 服务

在本步骤中，你将编写你的第一个 Ansible Playbook。Playbook 是一个用 YAML 格式编写的文件，它描述了要在托管主机上执行的一系列任务。你将创建一个 Playbook，该 Playbook 将在你的 inventory 中定义的 `localhost` 机器上安装 Apache Web 服务器（`httpd`）并启动其服务。

你应该仍然处于 `~/project/ansible-lab` 目录中。

1. 首先，使用 `nano` 文本编辑器创建一个名为 `apache.yml` 的新文件。此文件将包含你的 Playbook。

    ```bash
    nano apache.yml
    ```

2. 在 `nano` 编辑器中，你将定义一个“play”。Play 是 Playbook 的核心单元，它将一组主机映射到一组任务。将以下内容添加到 `apache.yml`。

    - `---`: 这是一个标准的 YAML 标记，表示文档的开始。
    - `- name: ...`: 这是你的 play 的开始。给它一个描述性的名称是一种最佳实践。
    - `hosts: webservers`: 这告诉 Ansible 在你的 inventory 文件中的 `webservers` 组中的所有主机上运行此 play。
    - `become: true`: 这指示 Ansible 使用权限提升（如 `sudo`）来执行任务。这对于安装软件或管理服务等操作是必需的。
    - `tasks:`: 这个关键字开始要执行的任务列表。

    ```yaml
    ---
    - name: Install and start Apache web server
      hosts: webservers
      become: true
      tasks:
    ```

3. 现在，将任务添加到你的 Playbook 中。每个任务都是一个调用 Ansible 模块的单一操作。**YAML 中的缩进至关重要**，因此请确保任务在 `tasks:` 部分下正确缩进。

    - **任务 1：安装 httpd。** 此任务使用 `ansible.builtin.dnf` 模块来确保安装 `httpd` 包。`state: present` 参数意味着如果包缺失，Ansible 将安装它；如果已安装，则不执行任何操作。
    - **任务 2：启动 httpd 服务。** 此任务使用 `ansible.builtin.service` 模块。`state: started` 确保服务正在运行，而 `enabled: true` 确保它将在系统启动时自动启动。

    将以下任务添加到你的 `apache.yml` 文件中，直接放在 `tasks:` 行下方：

    ```yaml
    - name: Install httpd package
      ansible.builtin.dnf:
        name: httpd
        state: present

    - name: Start and enable httpd service
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: true
    ```

4. 你完整的 `apache.yml` Playbook 现在应该如下所示。请仔细检查缩进。

    ```yaml
    ---
    - name: Install and start Apache web server
      hosts: webservers
      become: true
      tasks:
        - name: Install httpd package
          ansible.builtin.dnf:
            name: httpd
            state: present

        - name: Start and enable httpd service
          ansible.builtin.service:
            name: httpd
            state: started
            enabled: true
    ```

    保存文件并退出 `nano`（`Ctrl+O`，`Enter`，`Ctrl+X`）。

5. 在运行你的 Playbook 之前，最好使用 `ansible-playbook` 命令和 `--syntax-check` 标志来检查其语法错误。

    ```bash
    ansible-playbook --syntax-check apache.yml
    ```

    如果语法正确，该命令将打印 Playbook 的文件名而不会出现任何错误。

    ```plaintext
    playbook: apache.yml
    ```

6. 现在，执行 Playbook。

    ```bash
    ansible-playbook apache.yml
    ```

    Ansible 将执行这些任务。由于这是第一次运行，你将看到两个任务的状态都显示为 `changed`，这表明系统状态已被修改。

    ```plaintext
    PLAY [Install and start Apache web server] *************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Install httpd package] ***************************************************
    changed: [localhost]

    TASK [Start and enable httpd service] ******************************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

7. 最后，通过使用 `curl` 从 `localhost` 请求默认网页来验证 Apache Web 服务器是否正在运行。

    ```bash
    curl http://localhost
    ```

    你应该会看到默认的 Apache 测试页面，这证实了你的 Playbook 已成功运行。

    ```plaintext
    <html>
      <head>
        <title>Test Page</title>
      </head>
      <body>
        <h1>Test Page</h1>
        <p>This is the default test page for the Apache HTTP server.</p>
      </body>
    </html>
    ```
