# 使用 changed_when 和 failed_when 控制任务状态

在本步骤中，你将学习如何更精细地控制 Ansible 如何解释任务的执行结果。你将了解两个强大的指令：`changed_when` 和 `failed_when`。

- `changed_when`: 默认情况下，像 `ansible.builtin.command` 或 `ansible.builtin.shell` 这样的模块几乎总是报告“changed”状态，即使它们运行的命令没有改变系统。`changed_when` 允许你定义一个自定义条件，以确定任务是否应报告为“changed”。这对于编写幂等（idempotent）playbook 和准确触发 handlers 至关重要。
- `failed_when`: 有时，即使命令的执行结果是可以接受的，命令也可能以非零退出状态码退出（Ansible 将此视为失败）。`failed_when` 允许你覆盖默认的失败条件，使你的 playbook 可以基于更智能的标准继续执行，例如命令的输出或特定的退出码。

让我们开始设置一个新的项目目录。

```bash
cd ~/project
mkdir control-state-lab
cd control-state-lab
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

### 使用 `changed_when`

首先，让我们看看命令任务默认的行为。我们将创建一个运行 `date` 命令的 playbook。此命令仅打印日期，不改变系统，但 `command` 模块会将其报告为更改。

创建一个名为 `playbook.yml` 的新 playbook。

```bash
nano playbook.yml
```

输入以下内容：

```yaml
---
- name: Control Task State
  hosts: localhost
  tasks:
    - name: Check local time (default behavior)
      ansible.builtin.command: date
```

保存并退出。现在，运行 playbook。

```bash
ansible-playbook -i inventory playbook.yml
```

在输出中注意到，该任务被报告为 `changed=1`，即使系统上没有任何内容被修改。

```plaintext
...
TASK [Check local time (default behavior)] *************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    ...
```

现在，让我们使用 `changed_when` 来告诉 Ansible 此命令永远不会改变系统。修改 `playbook.yml`。

```bash
nano playbook.yml
```

向任务添加 `changed_when: false`。

```yaml
---
- name: Control Task State
  hosts: localhost
  tasks:
    - name: Check local time (with changed_when)
      ansible.builtin.command: date
      changed_when: false
```

保存并退出。再次运行 playbook。

```bash
ansible-playbook -i inventory playbook.yml
```

这次，任务报告为 `ok`，最终的 recap 显示 `changed=0`。你已成功覆盖了默认行为。

```plaintext
...
TASK [Check local time (with changed_when)] ************************************
ok: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    ...
```

### 使用 `failed_when`

接下来，让我们探索 `failed_when`。我们将创建一个检查不存在文件的任务。命令默认会“失败”。

首先，创建一个虚拟文件进行搜索。

```bash
echo "System is running" > status.txt
```

现在，修改 `playbook.yml` 以在文件中搜索单词“ERROR”。`grep` 命令将以退出码 1 退出，因为找不到该单词，Ansible 将其解释为失败。

```bash
nano playbook.yml
```

用以下内容替换：

```yaml
---
- name: Control Task State
  hosts: localhost
  tasks:
    - name: Check for ERROR in status file (will fail)
      ansible.builtin.command: grep ERROR status.txt
```

保存并退出。运行 playbook。

```bash
ansible-playbook -i inventory playbook.yml
```

正如预期的那样，playbook 执行因 `FAILED!` 消息而停止。

```plaintext
...
TASK [Check for ERROR in status file (will fail)] ******************************
fatal: [localhost]: FAILED! => {"changed": true, "cmd": ["grep", "ERROR", "status.txt"], "delta": "...", "end": "...", "msg": "non-zero return code", "rc": 1, ...}
...
```

这不是我们想要的。对我们来说，“ERROR”的缺失是一个成功的条件。我们可以使用 `failed_when` 来重新定义什么构成失败。我们将告诉 Ansible，只有当命令的返回码大于 1 时才算失败。返回码 1（未找到模式）现在将被视为成功。我们还需要 `register` 任务的结果来检查其返回码 (`rc`)。

最后一次修改 `playbook.yml`。

```bash
nano playbook.yml
```

使用 `register` 和 `failed_when` 更新 playbook。

```yaml
---
- name: Control Task State
  hosts: localhost
  tasks:
    - name: Check for ERROR in status file (with failed_when)
      ansible.builtin.command: grep ERROR status.txt
      register: grep_result
      failed_when: grep_result.rc > 1
      changed_when: false
```

我们还添加了 `changed_when: false`，因为 `grep` 是一个只读操作，不会改变系统。

保存并退出。运行最终的 playbook。

```bash
ansible-playbook -i inventory playbook.yml
```

成功！该任务现在报告为 `ok`，因为其返回码为 1，不满足我们新的失败条件 (`rc > 1`)。Playbook 成功完成。

```plaintext
...
TASK [Check for ERROR in status file (with failed_when)] ***********************
ok: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    ...
```

你现在已经学会了如何使用 `changed_when` 和 `failed_when` 来精确定义任务的成功、更改和失败状态，从而实现更健壮和智能的自动化。
