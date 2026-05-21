# 使用 Ansible Vault 的加密变量创建系统用户

在这一步中，你将学习如何使用 Ansible Vault 管理敏感数据，例如密码或 API 密钥。在 Playbook 中以明文形式存储敏感信息是一个重大的安全风险。Ansible Vault 提供了一种加密文件或单个变量的方法，从而保护你的机密信息。你可以在 Playbook 中使用这些加密文件，当提供正确的密码时，Ansible 会在运行时解密它们。

我们将创建一个包含用户名和哈希密码的加密文件，然后使用 Playbook 创建一个带有这些凭据的新系统用户。

1. **导航至项目目录**

    确保你处于此任务的 `~/project` 目录中。

    ```bash
    cd ~/project
    ```

2. **创建加密的 Vault 文件**

    我们将使用 `ansible-vault create` 命令创建一个名为 `secrets.yml` 的新加密 YAML 文件。此命令将提示你为 Vault 创建一个密码。以后打开、编辑或使用该文件时都需要此密码。

    首先，将编辑器设置为 `nano` 以方便操作：

    ```bash
    export EDITOR=nano
    ```

    现在创建 Vault 文件：

    ```bash
    ansible-vault create secrets.yml
    ```

    出现提示时，输入 Vault 密码。在本实验中，为了简单起见，我们使用 `labex` 作为 Vault 密码。你需要输入两次。

    ```plaintext
    New Vault password:
    Confirm New Vault password:
    ```

    确认密码后，该命令将在 `nano` 文本编辑器中打开 `secrets.yml` 文件。

3. **向 Vault 文件添加机密变量**

    在 `nano` 编辑器中（此时正在编辑加密的 `secrets.yml` 文件），添加以下变量。我们将为新用户定义一个用户名和一个预哈希的密码。使用哈希密码比存储明文密码安全得多。

    ```yaml
    username: myappuser
    pwhash: $6$mysalt$QwMzWSEyCAGmz7tzVrAi5o.8k4d05i2QsfGGwmPtlJsWhGjSjCW6yFCH/OEqEsHk7GMSxqYNXu5sshxPmWyxo0
    ```

    - `username`：我们要创建的系统用户的名称。
    - `pwhash`：一个安全哈希后的密码。此特定哈希对应于密码 `AnsibleUserP@ssw0rd`，并且是 `ansible.builtin.user` 模块可以理解的格式。

    保存文件并退出 `nano`（`Ctrl+X`，然后 `Y`，然后 `Enter`）。你 `~/project` 目录中的 `secrets.yml` 文件现在已加密。如果你尝试使用 `cat secrets.yml` 查看它，你只会看到加密文本。

4. **创建使用 Vault 文件的 Playbook**

    现在，创建一个名为 `create_user.yml` 的新 Playbook，它将使用来自加密 `secrets.yml` 文件的变量。

    ```bash
    nano create_user.yml
    ```

    添加以下内容。`vars_files` 指令告诉 Ansible 从指定文件加载变量。

    ```yaml
    ---
    - name: Create a user from secret variables
      hosts: localhost
      become: true
      vars_files:
        - secrets.yml
      tasks:
        - name: Create the {{ username }} user
          ansible.builtin.user:
            name: "{{ username }}"
            password: "{{ pwhash }}"
            state: present
    ```

    此 Playbook 将创建一个具有 `secrets.yml` 中定义的名称和密码哈希的用户。保存文件并退出 `nano`。

5. **使用 Vault 密码运行 Playbook**

    要运行使用 Vault 文件的 Playbook，你必须提供 Vault 密码。你可以使用 `--ask-vault-pass` 标志以交互方式执行此操作。

    ```bash
    ansible-playbook --ask-vault-pass create_user.yml
    ```

    Ansible 将提示你输入 Vault 密码。输入 `labex`（你在第 2 步中设置的密码）。

    ```plaintext
    Vault password:
    ```

    提供正确的密码后，Ansible 将在内存中解密该文件并运行 Playbook。你应该会看到以下输出，指示用户已创建。

    ```plaintext
    PLAY [Create a user from secret variables] *************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Create the myappuser user] ***********************************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

6. **验证用户是否已创建**

    你可以使用 `id` 命令确认 `myappuser` 已在系统上成功创建。

    ```bash
    id myappuser
    ```

    如果用户存在，你将看到其用户 ID (uid) 和组 ID (gid) 信息。

    ```plaintext
    uid=1002(myappuser) gid=1002(myappuser) groups=1002(myappuser)
    ```

    这确认了你已成功使用 Ansible Vault 为自动化任务管理敏感数据。
