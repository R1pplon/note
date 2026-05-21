# 使用 Ansible 事实显示系统信息

在这一步中，你将探索 Ansible 事实（Facts）。事实是 Ansible 收集的关于其管理系统（在本例中为 `localhost`）的信息片段。这些信息包括操作系统、网络接口、内存等详细信息。默认情况下，Ansible 会在每个 Play 开始时收集事实，并将它们存储在一个名为 `ansible_facts` 的特殊变量中。使用事实可以让你创建能够适应运行环境的动态 Playbook。

1. **导航至项目目录**

    首先，确保你处于将要创建新 Playbook 的 `~/project` 目录中。

    ```bash
    cd ~/project
    ```

2. **创建显示所有事实的 Playbook**

    让我们从创建一个仅显示 Ansible 可收集的关于你系统所有事实的 Playbook 开始。这将让你了解可用的海量信息。使用 `nano` 创建一个名为 `display_facts.yml` 的新文件。

    ```bash
    nano display_facts.yml
    ```

    在 `nano` 编辑器中，添加以下内容。此 Playbook 针对 `localhost`，并使用 `ansible.builtin.debug` 模块打印 `ansible_facts` 变量的内容。

    ```yaml
    ---
    - name: Display all Ansible facts
      hosts: localhost
      tasks:
        - name: Print all available facts
          ansible.builtin.debug:
            var: ansible_facts
    ```

    保存文件并按 `Ctrl+X`、`Y` 和 `Enter` 退出 `nano`。

3. **运行 Playbook**

    现在，执行 Playbook 以查看结果。

    ```bash
    ansible-playbook display_facts.yml
    ```

    输出会非常长，因为 Ansible 收集了大量数据。它将是一个包含所有系统详细信息的大型 JSON 结构。这是预期的结果。

    ```plaintext
    PLAY [Display all Ansible facts] ***********************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Print all available facts] ***********************************************
    ok: [localhost] => {
        "ansible_facts": {
            "ansible_all_ipv4_addresses": [
                "172.17.0.2"
            ],
            "ansible_all_ipv6_addresses": [
                "fe80::42:acff:fe11:2"
            ],
            "ansible_apparmor": {
                "status": "disabled"
            },
            "ansible_architecture": "x86_64",
            "ansible_bios_date": "01/01/2011",
            "ansible_bios_version": "1.0",
            "ansible_cmdline": {
                "BOOT_IMAGE": "/boot/vmlinuz-5.14.0-427.16.1.el9_4.x86_64",
                "root": "UUID=...",
                "ro": true
            },
            "ansible_date_time": {
                "date": "2024-05-21",
                "day": "21",
                "epoch": "1716298855",
                ...
            },
            "ansible_distribution": "RedHat",
            "ansible_distribution_major_version": "9",
            "ansible_distribution_version": "9.4",
            ...
        }
    }

    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

4. **创建显示特定事实的 Playbook**

    显示所有事实对于发现信息很有用，但在大多数情况下，你只需要特定的信息片段。让我们创建另一个 Playbook `display_specific_facts.yml`，以显示包含几个关键事实的格式化消息。

    ```bash
    nano display_specific_facts.yml
    ```

    添加以下内容。此 Playbook 使用 `debug` 模块的 `msg` 参数来打印自定义字符串。我们使用括号表示法访问单个事实，例如 `ansible_facts['distribution']`。

    ```yaml
    ---
    - name: Display specific Ansible facts
      hosts: localhost
      tasks:
        - name: Print a summary of system facts
          ansible.builtin.debug:
            msg: >
              The operating system is {{ ansible_facts['distribution'] }}
              version {{ ansible_facts['distribution_major_version'] }}.
              It has {{ ansible_facts['processor_cores'] }} processor cores and
              {{ ansible_facts['memtotal_mb'] }} MB of total memory.
    ```

    `msg: >` 中的 `>` 字符是一个 YAML 特性，允许你更整洁地编写多行字符串。保存文件并退出 `nano`。

5. **运行显示特定事实的 Playbook**

    现在，运行这个新 Playbook。

    ```bash
    ansible-playbook display_specific_facts.yml
    ```

    输出将更加简洁易读，仅显示你请求的信息。

    ```plaintext
    PLAY [Display specific Ansible facts] ******************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Print a summary of system facts] *****************************************
    ok: [localhost] => {
        "msg": "The operating system is RedHat version 9. It has 2 processor cores and 3925 MB of total memory."
    }

    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

    这展示了如何利用 Ansible 事实使你的 Playbook 能够感知其运行环境，从而实现更智能、更具条件性的自动化。
