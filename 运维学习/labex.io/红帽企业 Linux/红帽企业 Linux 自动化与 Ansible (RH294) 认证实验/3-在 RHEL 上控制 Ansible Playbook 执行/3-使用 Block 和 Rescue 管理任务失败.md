# 使用 Block 和 Rescue 管理任务失败

在本步骤中，你将学习如何在 Ansible playbook 中优雅地处理错误。默认情况下，如果任何任务失败，Ansible 将停止在该宿主上执行整个 playbook。虽然这是一个安全的默认设置，但有时你需要更多的控制。你将探索两种错误处理方法：简单的 `ignore_errors` 指令以及更强大的 `block`、`rescue` 和 `always` 结构，它提供了一种尝试执行任务并在它们失败时定义恢复操作的方法。

首先，让我们为本次实验创建一个新目录。

```bash
cd ~/project
mkdir control-errors-lab
cd control-errors-lab
```

为 `localhost` 创建标准的 `inventory` 文件。

```bash
nano inventory
```

添加以下内容：

```ini
localhost ansible_connection=local
```

保存并退出编辑器 (`Ctrl+X`, `Y`, `Enter`)。

现在，让我们创建一个名为 `playbook.yml` 的 playbook，该 playbook 被设计为会失败。第一个任务将尝试安装一个不存在的软件包。

```bash
nano playbook.yml
```

输入以下内容。此 playbook 尝试安装一个假的 `httpd-fake` 软件包，然后安装一个真实的软件包 `mariadb-server`。

```yaml
---
- name: Demonstrate Task Failure
  hosts: localhost
  become: yes
  tasks:
    - name: Attempt to install a non-existent package
      ansible.builtin.dnf:
        name: httpd-fake
        state: present

    - name: Install MariaDB server
      ansible.builtin.dnf:
        name: mariadb-server
        state: present
```

保存并退出编辑器。现在，运行 playbook。

```bash
ansible-playbook -i inventory playbook.yml
```

你将看到第一个任务因错误消息而失败，因为找不到 `httpd-fake` 软件包。至关重要的是，Ansible 将停止执行，第二个任务“Install MariaDB server”将不会被执行。

```plaintext
...
TASK [Attempt to install a non-existent package] *******************************
fatal: [localhost]: FAILED! => {"changed": false, "msg": "No match for argument: httpd-fake", "rc": 1, "results": []}

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
```

现在，让我们使用 `block` 和 `rescue` 来更优雅地处理此失败。`block` 关键字用于对一组任务进行分组。如果 `block` 中的任何任务失败，Ansible 将跳过 `block` 中其余的任务并执行 `rescue` 部分中的任务。无论 `block` 或 `rescue` 部分成功还是失败，`always` 部分都会运行。

修改 `playbook.yml` 以使用此结构。

```bash
nano playbook.yml
```

用以下内容替换整个文件。在这里，我们在 `block` 中尝试安装假的软件包。当它失败时，将运行 `rescue` 部分，将 `mariadb-server` 作为恢复步骤进行安装。`always` 部分将在最后打印一条消息。

```yaml
---
- name: Handle Task Failure with Block and Rescue
  hosts: localhost
  become: yes
  tasks:
    - name: Attempt primary task, with recovery
      block:
        - name: Attempt to install a non-existent package
          ansible.builtin.dnf:
            name: httpd-fake
            state: present
        - name: This task will be skipped
          ansible.builtin.debug:
            msg: "This message will not appear because the previous task fails."
      rescue:
        - name: Install MariaDB server on failure
          ansible.builtin.dnf:
            name: mariadb-server
            state: present
      always:
        - name: This always runs
          ansible.builtin.debug:
            msg: "The block has completed, either by success or rescue."
```

保存并退出。再次运行 playbook。

```bash
ansible-playbook -i inventory playbook.yml
```

观察输出。`block` 中的第一个任务如预期失败。`block` 中的第二个任务被跳过。然后 Ansible 会进入 `rescue` 部分并成功安装 `mariadb-server`。最后，`always` 部分运行。

```plaintext
...
TASK [Attempt to install a non-existent package] *******************************
fatal: [localhost]: FAILED! => ...

TASK [This task will be skipped] ***********************************************
skipping: [localhost]

RESCUE START *******************************************************************

TASK [Install MariaDB server on failure] ***************************************
changed: [localhost]

ALWAYS START *******************************************************************

TASK [This always runs] ********************************************************
ok: [localhost] => {
    "msg": "The block has completed, either by success or rescue."
}

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=1    ignored=0
```

现在，让我们看看当 `block` 成功时会发生什么。编辑 playbook 并修复软件包名称。

```bash
nano playbook.yml
```

将 `httpd-fake` 改为一个真实的软件包 `httpd`。

```yaml
# ... (rest of the playbook)
block:
  - name: Attempt to install a valid package
    ansible.builtin.dnf:
      name: httpd # Corrected from httpd-fake
      state: present
  - name: This task will now run
    ansible.builtin.debug:
      msg: "This message will now appear because the previous task succeeds."
# ... (rest of the playbook)
```

保存并退出。最后一次运行 playbook。

```bash
ansible-playbook -i inventory playbook.yml
```

这次，`block` 中的两个任务都成功了。由于 `block` 在没有错误的情况下完成，因此 `rescue` 部分被完全跳过。如其名称所示，`always` 部分仍然运行。

```plaintext
...
TASK [Attempt to install a valid package] **************************************
changed: [localhost]

TASK [This task will now run] **************************************************
ok: [localhost] => {
    "msg": "This message will now appear because the previous task succeeds."
}

RESCUE START *******************************************************************
skipping rescue

ALWAYS START *******************************************************************

TASK [This always runs] ********************************************************
ok: [localhost] => {
    "msg": "The block has completed, either by success or rescue."
}

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

你现在已经成功使用了 `block`/`rescue`/`always` 结构来创建一个健壮的 playbook，该 playbook 可以处理失败并执行恢复操作。
