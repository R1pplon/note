# 验证 Web 服务器和用户配置

在最后一步中，你将通过创建一个专门的验证 Playbook 来巩固你的学习成果。到目前为止，你一直使用 `cat`、`id` 和 `groups` 等标准 Linux 命令手动检查 Playbook 的结果。一种更强大且可重复的方法是使用 Ansible 本身来审计和验证系统的状态。

此 Playbook 将充当测试套件，以编程方式检查 Web 服务器是否已安装、网页内容是否正确以及系统用户是否存在且具有正确的组成员身份。这展示了 Ansible 不仅可以用于配置管理，还可以用于合规性和状态验证。

1. **导航至项目目录**

    首先，确保你处于 `~/project` 目录中。

    ```bash
    cd ~/project
    ```

2. **创建验证 Playbook**

    让我们创建一个名为 `verify_config.yml` 的新 Playbook。此 Playbook 将包含一系列任务，用于检查你在之前步骤中应用的配置。

    ```bash
    nano verify_config.yml
    ```

3. **添加任务以验证配置**

    在 `nano` 编辑器中，添加以下内容。我们将构建一个包含多个任务的 Playbook，每个任务都旨在断言某个特定条件为真。如果任何断言失败，Playbook 将停止并报告错误，立即告诉你哪里出了问题。

    ```yaml
    ---
    - name: Verify system configuration
      hosts: localhost
      become: true
      tasks:
        - name: Check if httpd package is installed
          ansible.builtin.dnf:
            list: httpd
          register: httpd_pkg_info

        - name: Assert that httpd is installed
          ansible.builtin.assert:
            that:
              - httpd_pkg_info.results | length > 0
            fail_msg: "Apache (httpd) package is not installed."
            success_msg: "Apache (httpd) package is installed."

        - name: Read the content of the index.html file
          ansible.builtin.slurp:
            src: /var/www/html/index.html
          register: index_file

        - name: Assert that the web page content is correct
          ansible.builtin.assert:
            that:
              - "'Custom Facts' in (index_file.content | b64decode)"
            fail_msg: "Web page content is incorrect."
            success_msg: "Web page content is correct."

        - name: Check if myappuser exists
          ansible.builtin.getent:
            database: passwd
            key: myappuser
          register: user_info

        - name: Assert that myappuser exists
          ansible.builtin.assert:
            that:
              - user_info.ansible_facts.getent_passwd['myappuser'] is defined
            fail_msg: "User 'myappuser' does not exist."
            success_msg: "User 'myappuser' exists."

        - name: Query the wheel group members
          ansible.builtin.getent:
            database: group
            key: wheel
          register: wheel_group_info

        - name: Assert that myappuser is in the wheel group
          ansible.builtin.assert:
            that:
              - "'myappuser' in (wheel_group_info.ansible_facts.getent_group['wheel'][2] | default('') | split(','))"
            fail_msg: "User 'myappuser' is not in the wheel group."
            success_msg: "User 'myappuser' is in the wheel group."
    ```

    让我们回顾一下这里使用的关键模块：

    - `ansible.builtin.dnf` 配合 `list`：这会检查软件包并 `register`（注册）结果。
    - `ansible.builtin.slurp`：这会从远程主机“吸取”整个文件内容。内容经过 base64 编码以实现安全传输。
    - `ansible.builtin.getent`：这是一种查询 `passwd` 和 `group` 等系统数据库的安全方法。结果存储在 `ansible_facts` 下，因此通过 `user_info.ansible_facts.getent_passwd` 等键访问返回的数据。
    - `ansible.builtin.assert`：这是我们验证的核心。它检查给定条件是否为真。如果不是，则 Play 失败。我们提供了自定义的成功和失败消息。
    - `b64decode`：这是一个 Jinja2 过滤器，用于解码我们从 `slurp` 模块获得的 base64 内容。

    注意，我们分别查询 `passwd` 和 `group` 数据库。这使得用户存在性检查和 `wheel` 组成员身份检查与 `getent` 返回的实际数据保持一致。

    保存文件并退出 `nano`（`Ctrl+X`，`Y`，`Enter`）。

4. **运行验证 Playbook**

    现在，执行你的验证 Playbook。由于它不使用任何 Vault 文件，因此无需提供密码。

    ```bash
    ansible-playbook verify_config.yml
    ```

    如果之前的所有步骤都已正确完成，Playbook 将成功运行，你将看到每个断言的自定义成功消息。

    ```plaintext
    PLAY [Verify system configuration] *********************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Check if httpd package is installed] *************************************
    ok: [localhost]

    TASK [Assert that httpd is installed] ******************************************
    ok: [localhost] => {
        "changed": false,
        "msg": "Apache (httpd) package is installed."
    }

    TASK [Read the content of the index.html file] *********************************
    ok: [localhost]

    TASK [Assert that the web page content is correct] *****************************
    ok: [localhost] => {
        "changed": false,
        "msg": "Web page content is correct."
    }

    TASK [Check if myappuser exists] ***********************************************
    ok: [localhost]

    TASK [Assert that myappuser exists] ********************************************
    ok: [localhost] => {
        "changed": false,
        "msg": "User 'myappuser' exists."
    }

    TASK [Query the wheel group members] *******************************************
    ok: [localhost]

    TASK [Assert that myappuser is in the wheel group] *****************************
    ok: [localhost] => {
        "changed": false,
        "msg": "User 'myappuser' is in the wheel group."
    }

    PLAY RECAP *********************************************************************
    localhost                  : ok=9    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

    恭喜！你已成功使用 Ansible 定义变量、收集系统事实、使用 Vault 管理机密信息，并最终以自动化的方式验证了系统的状态。
