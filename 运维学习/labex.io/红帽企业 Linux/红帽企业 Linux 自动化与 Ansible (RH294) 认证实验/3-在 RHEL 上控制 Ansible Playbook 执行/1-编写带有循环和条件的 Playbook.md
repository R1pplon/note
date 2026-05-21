# 编写带有循环和条件的 Playbook

在本步骤中，你将学习 Ansible 中控制任务执行的两个基本概念：循环（loops）和条件语句（conditionals）。循环允许你使用不同的值重复执行某个任务，这对于安装多个软件包或创建多个用户等任务非常高效。条件语句使用 `when` 关键字，允许你仅在满足特定条件时才执行任务，例如操作系统是特定版本或文件已存在。

首先，让我们确保你的 LabEx VM 上已安装 Ansible。我们将使用 DNF 包管理器来完成此操作。

```bash
sudo dnf install -y ansible-core
```

你应该会看到输出表明 `ansible-core` 及其依赖项正在安装。

```plaintext
...
Installed:
  ansible-core-2.x.x-1.el9.x86_64
  ...
Complete!
```

现在，让我们设置我们的项目目录。本次实验的所有工作都将在一个专用目录中进行，以保持整洁。

```bash
cd ~/project
mkdir control-flow-lab
cd control-flow-lab
```

Ansible 项目需要一个清单文件（inventory file），该文件定义了你要管理的宿主。对于本次实验，我们将管理本地机器 `localhost`。

使用 `nano` 编辑器创建一个名为 `inventory` 的清单文件：

```bash
nano inventory
```

向文件中添加以下行。这会告诉 Ansible 在 `localhost` 上运行 playbook，并直接连接到它，而不是使用 SSH。

```ini
localhost ansible_connection=local
```

保存文件并按 `Ctrl+X`，然后按 `Y`，最后按 `Enter` 退出 `nano`。

接下来，我们将创建第一个 playbook `playbook.yml` 来演示循环。此 playbook 将安装一系列有用的命令行工具。

```bash
nano playbook.yml
```

在编辑器中输入以下 YAML 内容。此 playbook 定义了一个使用 `ansible.builtin.dnf` 模块安装软件包的任务。`become: yes` 指令告诉 Ansible 使用 `sudo` 权限执行任务，这对于安装软件包是必需的。`loop` 关键字提供了一个软件包名称列表。Ansible 将为列表中的每个项运行此任务一次，将 `{{ item }}` 占位符替换为当前的软件包名称。

```yaml
---
- name: Install common tools
  hosts: localhost
  become: yes
  tasks:
    - name: Install specified packages
      ansible.builtin.dnf:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - tree
        - wget
```

保存并退出编辑器。现在，使用 `ansible-playbook` 命令运行 playbook，并使用 `-i` 标志指定你的清单文件。

```bash
ansible-playbook -i inventory playbook.yml
```

输出将显示 playbook 的执行情况。Ansible 将检查每个软件包，并在其尚未安装时进行安装。最后的 `PLAY RECAP` 总结了结果。

```plaintext
PLAY [Install tools and run conditional tasks] *********************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Install specified packages] **********************************************
changed: [localhost] => (item=git)
changed: [localhost] => (item=tree)
changed: [localhost] => (item=wget)

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

现在，让我们修改 playbook 以包含一个条件任务。我们将添加一个任务，该任务将打印一条消息，但仅当操作系统是 Red Hat Enterprise Linux 时。这是根据特定环境定制自动化的常见用例。

再次打开 `playbook.yml` 文件：

```bash
nano playbook.yml
```

将以下任务添加到文件末尾。`when` 关键字会评估给定的表达式。`ansible_facts['distribution']` 是 Ansible 自动发现的关于被管理宿主的一个变量。第一个任务将运行，因为我们的环境是 RHEL，而第二个任务将被跳过。

```yaml
---
- name: Install tools and run conditional tasks
  hosts: localhost
  become: yes
  tasks:
    - name: Install specified packages
      ansible.builtin.dnf:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - tree
        - wget

    - name: Show message on Red Hat systems
      ansible.builtin.debug:
        msg: "This system is a Red Hat family distribution."
      when: ansible_facts['distribution'] == "RedHat"

    - name: Show message on other systems
      ansible.builtin.debug:
        msg: "This system is NOT a Red Hat family distribution."
      when: ansible_facts['distribution'] != "RedHat"
```

保存并退出编辑器。运行更新后的 playbook：

```bash
ansible-playbook -i inventory playbook.yml
```

仔细观察输出。由于软件包已安装，软件包安装任务可能会报告所有项都为 `ok`。更重要的是，你将看到第一个调试消息被打印出来，而第二个消息被标记为 `skipping`。

```plaintext
PLAY [Install tools and run conditional tasks] *********************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Install specified packages] **********************************************
ok: [localhost] => (item=git)
ok: [localhost] => (item=tree)
ok: [localhost] => (item=wget)

TASK [Show message on Red Hat systems] *****************************************
ok: [localhost] => {
    "msg": "This system is a Red Hat family distribution."
}

TASK [Show message on other systems] *******************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

你已成功编写并执行了一个 Ansible playbook，该 playbook 使用循环执行重复性操作，并使用条件语句根据系统事实控制任务执行。
