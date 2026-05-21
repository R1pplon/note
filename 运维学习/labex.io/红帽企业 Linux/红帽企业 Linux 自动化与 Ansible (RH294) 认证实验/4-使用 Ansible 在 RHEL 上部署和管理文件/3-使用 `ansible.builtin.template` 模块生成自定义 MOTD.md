# 使用 `ansible.builtin.template` 模块生成自定义 MOTD

在本步骤中，你将从复制静态文件进阶到使用 `ansible.builtin.template` 模块生成动态文件。该模块利用 Jinja2 模板引擎，根据 Ansible 从托管主机收集的变量和系统信息（称为“facts”）来创建定制化文件。我们将创建一个动态的每日消息 (MOTD)，以显示特定于系统的相关信息。

1. **首先，确保你在 `~/project` 目录中，并创建一个专门用于存放模板的子目录。** 将 Jinja2 模板存储在 `templates` 目录中是 Ansible 的标准最佳实践。

    ```bash
    cd ~/project
    mkdir templates
    ```

2. **接下来，创建 Jinja2 模板文件。** 这个文件 `motd.j2` 将包含我们 MOTD 的结构，其中包含动态数据的占位符。`.j2` 扩展名是 Jinja2 模板的常用约定。

    ```bash
    nano ~/project/templates/motd.j2
    ```

    将以下内容添加到文件中。请注意 `{{ ... }}` 语法，它表示一个变量或 fact 的占位符。

    ```jinja2
    #################################################################
    #          Welcome to {{ ansible_facts['fqdn'] }}
    #
    # This is a {{ ansible_facts['distribution'] }} system.
    # System managed by Ansible.
    #
    # For support, contact: {{ admin_email }}
    #################################################################
    ```

    在此模板中：

    - `{{ ansible_facts['fqdn'] }}` 将被替换为主机的完全限定域名 (Fully Qualified Domain Name)。
    - `{{ ansible_facts['distribution'] }}` 将被替换为 Linux 发行版的名称（例如 RedHat）。
    - `{{ admin_email }}` 是一个自定义变量，我们将在 playbook 中定义它。

3. **现在，创建一个名为 `template_motd.yml` 的新 playbook**。此 playbook 将使用模板生成 `/etc/motd` 文件。

    ```bash
    nano ~/project/template_motd.yml
    ```

    添加以下内容。此 playbook 需要提升的权限 (`become: true`) 才能写入 `/etc` 目录。它还定义了自定义的 `admin_email` 变量。

    ```yaml
    ---
    - name: Deploy a custom MOTD from a template
      hosts: localhost
      become: true
      vars:
        admin_email: admin@labex.io
      tasks:
        - name: Generate /etc/motd from template
          ansible.builtin.template:
            src: templates/motd.j2
            dest: /etc/motd
            owner: root
            group: root
            mode: "0644"
    ```

    此 playbook 中的关键参数：

    - `become: true`: 这告诉 Ansible 使用 `sudo` 来执行任务，这对于写入 `/etc/motd` 是必需的。
    - `vars`: 此部分是我们定义自定义变量（如 `admin_email`）的地方。
    - `ansible.builtin.template`: 处理 Jinja2 模板的模块。`src` 指向我们的 `.j2` 文件，而 `dest` 是托管主机上的目标文件。

4. **执行 playbook。**

    ```bash
    ansible-playbook -i inventory.ini template_motd.yml
    ```

    输出应确认任务已成功完成。

    ```plaintext
    PLAY [Deploy a custom MOTD from a template] ************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Generate /etc/motd from template] ****************************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

5. **验证结果。** 检查新生成的 `/etc/motd` 文件的内容。

    ```bash
    cat /etc/motd
    ```

    你将看到渲染后的输出，其中 Jinja2 占位符已被实际的系统 facts 和你定义的自定义变量替换。`fqdn` 将与你的实验环境的主机名匹配。

    ```plaintext
    #################################################################
    #          Welcome to host.labex.io
    #
    # This is a RedHat system.
    # System managed by Ansible.
    #
    # For support, contact: admin@labex.io
    #################################################################
    ```

你现在已经成功使用模板创建了一个定制化文件，这是基础设施自动化中的一项核心技能。
