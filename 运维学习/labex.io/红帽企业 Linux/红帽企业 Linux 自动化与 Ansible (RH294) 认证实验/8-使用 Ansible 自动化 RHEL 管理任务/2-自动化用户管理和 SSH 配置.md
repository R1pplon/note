# 自动化用户管理和 SSH 配置

在本步骤中，你将学习如何使用 Ansible 自动化用户账户管理、SSH 配置和 sudo 权限。这对于在你的基础设施中维护一致的用户访问和安全策略至关重要。

你将使用诸如 `ansible.builtin.user` 用于用户管理，`ansible.builtin.group` 用于组创建，`ansible.posix.authorized_key` 用于 SSH 密钥管理，以及 `ansible.builtin.lineinfile` 用于配置文件修改等模块。

1. **导航到新的项目目录以进行用户管理任务。**

    ```bash
    cd ~/project
    mkdir system-users
    cd system-users
    ```

    安装 `ansible.posix` collection。

    ```bash
    ansible-galaxy collection install ansible.posix
    ```

2. **创建本次实验的 inventory 文件**。

    ```bash
    cat << EOF > inventory.ini
    [webservers]
    localhost ansible_connection=local
    EOF
    ```

3. **创建一个变量文件** 来定义我们要管理的用户的组和用户。

    ```bash
    mkdir vars
    nano vars/users_vars.yml
    ```

    添加以下内容来定义用户账户及其组的成员关系：

    ```yaml
    ---
    users:
      - username: webuser1
        groups: webadmin
      - username: webuser2
        groups: webadmin
      - username: devuser1
        groups: webadmin
    ```

4. **为我们的用户生成 SSH 密钥对**。在实际环境中，用户会提供他们的公钥。

    ```bash
    mkdir files
    
    # 为每个用户生成 SSH 密钥
    ssh-keygen -t rsa -b 2048 -f files/webuser1.key -N "" -C "webuser1@example.com"
    ssh-keygen -t rsa -b 2048 -f files/webuser2.key -N "" -C "webuser2@example.com"
    ssh-keygen -t rsa -b 2048 -f files/devuser1.key -N "" -C "devuser1@example.com"
    ```

5. **创建主用户管理 playbook `users.yml`**。此 playbook 将创建组、用户，分发 SSH 密钥，并配置 sudo 访问。

    ```bash
    nano users.yml
    ```

    添加以下全面的用户管理 playbook：

    ```yaml
    ---
    - name: Create and manage user accounts
      hosts: webservers
      become: true
      vars_files:
        - vars/users_vars.yml
      tasks:
        - name: Create webadmin group
          ansible.builtin.group:
            name: webadmin
            state: present

        - name: Create user accounts
          ansible.builtin.user:
            name: "{{ item['username'] }}"
            groups: "{{ item['groups'] }}"
            shell: /bin/bash
            create_home: yes
            state: present
          loop: "{{ users }}"

        - name: Set up SSH authorized keys
          ansible.posix.authorized_key:
            user: "{{ item['username'] }}"
            key: "{{ lookup('file', 'files/' + item['username'] + '.key.pub') }}"
            state: present
          loop: "{{ users }}"

        - name: Configure sudo access for webadmin group
          ansible.builtin.lineinfile:
            path: /etc/sudoers.d/webadmin
            state: present
            create: yes
            mode: "0440"
            line: "%webadmin ALL=(ALL) NOPASSWD: ALL"
            validate: /usr/sbin/visudo -cf %s

        - name: Configure SSH to disable root login
          ansible.builtin.lineinfile:
            dest: /etc/ssh/sshd_config
            regexp: "^PermitRootLogin"
            line: "PermitRootLogin no"
            backup: yes
          notify: restart sshd

        - name: Configure SSH to disable password authentication
          ansible.builtin.lineinfile:
            dest: /etc/ssh/sshd_config
            regexp: "^PasswordAuthentication"
            line: "PasswordAuthentication no"
            backup: yes
          notify: restart sshd

      handlers:
        - name: restart sshd
          ansible.builtin.service:
            name: sshd
            state: restarted
    ```

    此 playbook 演示了几个用户管理最佳实践：

    - **组管理**：创建管理组
    - **用户创建**：设置用户账户及其正确的家目录
    - **SSH 密钥管理**：分发公钥以进行基于密钥的认证
    - **Sudo 配置**：安全地授予管理权限
    - **SSH 加固**：禁用 root 登录和密码认证
    - **服务管理**：在配置更改时重启 SSH 服务

6. **执行用户管理 playbook**。

    ```bash
    ansible-playbook -i inventory.ini users.yml
    ```

    Playbook 将创建用户，设置 SSH 密钥，配置 sudo 访问，并加固 SSH 配置。

7. **验证用户创建和组的成员关系**。

    ```bash
    # 检查用户是否已创建
    getent passwd webuser1 webuser2 devuser1
    
    # 检查组的成员关系
    groups webuser1
    groups webuser2
    groups devuser1
    
    # 验证 webadmin 组是否存在
    getent group webadmin
    ```

8. **测试其中一个已创建用户的 SSH 密钥认证**。

    ```bash
    # 测试 SSH 密钥认证（这将连接到 localhost）
    ssh -i files/webuser1.key webuser1@localhost "whoami"
    ```

9. **验证 sudo 配置**，通过测试无密码的 sudo 访问。

    ```bash
    # 测试 webuser1 的 sudo 访问
    ssh -i files/webuser1.key webuser1@localhost "sudo whoami"
    ```

10. **检查 SSH 配置更改**。

    ```bash
    # 验证 SSH 配置
    sudo grep "PermitRootLogin\|PasswordAuthentication" /etc/ssh/sshd_config
    
    # 检查 sudo 配置
    sudo cat /etc/sudoers.d/webadmin
    ```

你已成功使用 Ansible 自动化了用户账户创建、SSH 密钥分发和安全配置，为跨基础设施的安全用户管理奠定了基础。
