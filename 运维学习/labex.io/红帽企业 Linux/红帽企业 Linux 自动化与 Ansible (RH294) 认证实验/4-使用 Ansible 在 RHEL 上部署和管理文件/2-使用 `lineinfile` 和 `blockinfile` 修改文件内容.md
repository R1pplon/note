# 使用 `lineinfile` 和 `blockinfile` 修改文件内容

在本步骤中，你将学习如何在不替换整个文件的情况下修改托管主机上的现有文件。Ansible 为此目的提供了强大的模块：`ansible.builtin.lineinfile` 用于管理单行，`ansible.builtin.blockinfile` 用于管理多行文本块。这些模块在更改配置文件或向日志文件添加条目等任务中非常有用。

我们将继续使用你在上一步创建的 `info.txt` 文件，该文件位于 `/tmp/info.txt`。

1. **首先，确保你在项目目录中。**

    ```bash
    cd ~/project
    ```

2. **创建一个名为 `modify_file.yml` 的新 playbook**。这个 playbook 将包含两个任务：一个用于添加单行，另一个用于向现有文件添加文本块。

    ```bash
    nano ~/project/modify_file.yml
    ```

3. **将以下内容添加到你的 `modify_file.yml` playbook 中。** 这个 playbook 目标是 `localhost`，并使用 `lineinfile` 和 `blockinfile` 向 `/tmp/info.txt` 追加内容。

    ```yaml
    ---
    - name: Modify an existing file
      hosts: localhost
      tasks:
        - name: Add a single line of text to a file
          ansible.builtin.lineinfile:
            path: /tmp/info.txt
            line: This line was added by the lineinfile module.
            state: present

        - name: Add a block of text to an existing file
          ansible.builtin.blockinfile:
            path: /tmp/info.txt
            block: |
              # BEGIN ANSIBLE MANAGED BLOCK
              This block of text consists of two lines.
              They have been added by the blockinfile module.
              # END ANSIBLE MANAGED BLOCK
            state: present
    ```

    让我们来分析一下使用的模块：

    - `ansible.builtin.lineinfile`: 此模块确保文件中存在特定行。如果该行已存在，Ansible 将不执行任何操作，从而使任务具有幂等性。
      - `path`: 要修改的文件。
      - `line`: 要确保存在于文件中的文本行。
      - `state: present`: 这确保行存在。你可以使用 `state: absent` 来删除它。
    - `ansible.builtin.blockinfile`: 此模块管理一个文本块，该文本块由标记行（例如 `# BEGIN ANSIBLE MANAGED BLOCK`）包围。这对于管理配置部分非常理想。
      - `path`: 要修改的文件。
      - `block`: 要插入的多行字符串。`|` 是 YAML 中用于字面量块的语法，会保留换行符。
      - `state: present`: 确保块存在。

4. **使用 `ansible-playbook` 命令和你的 `inventory.ini` 文件执行 playbook。**

    ```bash
    ansible-playbook -i inventory.ini modify_file.yml
    ```

    输出将显示两个任务都对文件进行了更改。

    ```plaintext
    PLAY [Modify an existing file] *************************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Add a single line of text to a file] *************************************
    changed: [localhost]

    TASK [Add a block of text to an existing file] *********************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

5. **最后，通过查看 `/tmp/info.txt` 的内容来验证更改。**

    ```bash
    cat /tmp/info.txt
    ```

    你应该会看到原始内容，然后是新添加的行和新的文本块。

    ```plaintext
    This file was deployed by Ansible.
    It contains important system information.
    This line was added by the lineinfile module.
    # BEGIN ANSIBLE MANAGED BLOCK
    This block of text consists of two lines.
    They have been added by the blockinfile module.
    # END ANSIBLE MANAGED BLOCK
    ```

    如果你再次运行 playbook，Ansible 将报告 `ok=3` 和 `changed=0`，因为内容已存在，这展示了这些模块的幂等性。
