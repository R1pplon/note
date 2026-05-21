# 使用 Vault 密码文件运行 Playbook 以应用配置

在这一步中，你将学习一种向 Ansible 提供 Vault 密码的更自动化方式。在上一步中，你使用了 `--ask-vault-pass` 以交互方式输入密码。虽然这很安全，但不适合 CI/CD 流水线等没有用户在场输入密码的自动化环境。

解决方案是使用 Vault 密码文件。这是一个包含 Vault 密码的简单文本文件。运行 Playbook 时，你可以引用此文件，Ansible 将自动从中读取密码。为了安全起见，限制此密码文件的权限以确保只有授权用户才能读取它至关重要。

1. **导航至项目目录**

    确保你处于 Playbook 和 Vault 文件所在的 `~/project` 目录中。

    ```bash
    cd ~/project
    ```

2. **创建 Vault 密码文件**

    让我们创建一个文件来存储我们的 Vault 密码。将其命名为 `vault_pass.txt`。我们可以使用 `echo` 命令一步完成文件的创建并将密码 (`labex`) 写入其中。

    ```bash
    echo "labex" > vault_pass.txt
    ```

    你可以使用 `cat` 验证文件内容：

    ```bash
    cat vault_pass.txt
    ```

    输出应为：

    ```plaintext
    labex
    ```

3. **保护密码文件**

    将密码存储在纯文本文件中是有风险的。你必须限制其文件权限以保护它。`chmod` 命令允许你更改文件权限。我们将权限设置为 `600`，这意味着只有文件所有者（在本例中为 `labex` 用户）拥有读写权限。系统上的其他用户将无法访问它。

    ```bash
    chmod 600 vault_pass.txt
    ```

    你可以使用 `ls -l` 命令验证新权限：

    ```bash
    ls -l vault_pass.txt
    ```

    输出应以 `-rw-------` 开头，确认权限已受限。

    ```plaintext
    -rw-------. 1 labex labex 6 May 21 14:30 vault_pass.txt
    ```

4. **修改 Playbook 以将用户添加到组**

    让我们修改 `create_user.yml` Playbook 以执行额外操作。我们将把 `myappuser` 添加到 `wheel` 组，在许多系统上，这会授予管理（sudo）权限。这将演示运行一个对现有配置进行更改的 Playbook。

    首先，打开 `create_user.yml` Playbook 进行编辑。

    ```bash
    nano create_user.yml
    ```

    修改 `ansible.builtin.user` 任务以包含 `groups` 和 `append` 参数。

    ```yaml
    ---
    - name: Create a user from secret variables
      hosts: localhost
      become: true
      vars_files:
        - secrets.yml
      tasks:
        - name: Create the {{ username }} user and add to wheel group
          ansible.builtin.user:
            name: "{{ username }}"
            password: "{{ pwhash }}"
            state: present
            groups: wheel
            append: true
    ```

    - `groups: wheel`：指定要将用户添加到的组。
    - `append: true`：确保用户被添加到此组中，而不会将其从可能所属的任何其他组中移除。

    保存文件并退出 `nano`。

5. **使用 Vault 密码文件运行 Playbook**

    现在，再次运行 Playbook。这一次，不要使用 `--ask-vault-pass`，而是使用 `--vault-password-file` 选项（或其简写别名 `--vault-pass-file`）来指定密码文件的路径。

    ```bash
    ansible-playbook --vault-password-file vault_pass.txt create_user.yml
    ```

    Ansible 现在将运行而不会提示输入密码，因为它直接从 `vault_pass.txt` 中读取密码。你应该会看到指示用户配置已更改的输出。

    ```plaintext
    PLAY [Create a user from secret variables] *************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Create the myappuser user and add to wheel group] ************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

    `changed` 状态确认 Ansible 通过将用户添加到 `wheel` 组来修改了该用户。

6. **验证用户的组成员身份**

    最后，验证 `myappuser` 现在是否是 `wheel` 组的成员。你可以使用 `groups` 命令执行此操作。

    ```bash
    groups myappuser
    ```

    输出应同时显示用户的主组 (`myappuser`) 和 `wheel` 组。

    ```plaintext
    myappuser : myappuser wheel
    ```

    你已成功使用 Vault 密码文件以非交互方式运行 Playbook，这是自动化安全工作流的一项关键技能。
