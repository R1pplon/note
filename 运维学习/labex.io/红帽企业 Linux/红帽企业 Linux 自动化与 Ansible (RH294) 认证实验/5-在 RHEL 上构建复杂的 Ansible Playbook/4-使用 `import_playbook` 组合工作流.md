# 使用 `import_playbook` 组合工作流

在本步骤中，你将学习如何使用 `import_playbook` 来编排整个 Playbook，以形成复杂的工作流。虽然 `import_tasks` 和 `include_tasks` 用于在单个 Play 中重用任务列表，但 `import_playbook` 在更高级别上运行。它允许你创建一个主 Playbook，该 Playbook 按特定顺序执行其他独立的 Playbook。这是管理大规模自动化（例如，配置整个应用程序堆栈）的标准方法。

首先，让我们确保我们位于正确的目录中，并为这种新结构组织我们的项目：

```bash
cd ~/project/ansible_patterns
```

将单个组件 Playbook 存储在专用的子目录中是一种最佳实践。让我们创建一个名为 `playbooks` 的目录：

```bash
mkdir playbooks
```

现在，将我们在上一步中创建的配置 Web 服务器的 Playbook 移动到这个新目录中。将其重命名以使其更具描述性也是一个好主意：

```bash
mv playbook.yml playbooks/web_configure.yml
```

但是，由于我们将 Playbook 移动到了子目录中，因此我们需要更新任务文件的相对路径。任务文件仍然位于相对于主项目目录的 `tasks/` 目录中，因此我们需要调整路径：

```bash
nano playbooks/web_configure.yml
```

在 Playbook 中更新路径，使用 `../tasks/` 而不是 `tasks/`：

```yaml
---
- name: Configure Web Servers
  hosts: webservers
  gather_facts: false
  tasks:
    - name: Import web server setup tasks
      import_tasks: ../tasks/web_setup.yml

    - name: Include verification tasks
      include_tasks: ../tasks/verify_config.yml
```

保存并退出编辑器。

让我们测试已更正的 Playbook，以确保路径正常工作：

```bash
ansible-playbook playbooks/web_configure.yml -i inventory
```

你应该会看到 Playbook 使用更正的路径成功执行。

接下来，为配置数据库服务器创建一个新的独立 Playbook。此 Playbook 将定位 `dbservers` 组并安装 `mariadb` 包：

```bash
nano playbooks/db_setup.yml
```

将以下内容添加到文件中。这是一个完整、独立的 Play：

```yaml
---
- name: Configure Database Servers
  hosts: dbservers
  gather_facts: false
  tasks:
    - name: Install mariadb package
      ansible.builtin.dnf:
        name: mariadb
        state: present
      become: true

    - name: Display a confirmation message
      ansible.builtin.debug:
        msg: "Database server {{ inventory_hostname }} configured."
```

保存并退出编辑器。现在你有了两个组件 Playbook：一个用于 Web 服务器，一个用于数据库服务器。

最后，创建一个顶级的“主”Playbook。此文件本身不包含任何主机或任务。它的唯一工作是按正确的顺序导入其他 Playbook 来定义整体工作流：

```bash
nano main.yml
```

添加以下内容。这创建了一个工作流，该工作流首先配置 Web 服务器，然后配置数据库服务器：

```yaml
---
- name: Import the web server configuration play
  import_playbook: playbooks/web_configure.yml

- name: Import the database server configuration play
  import_playbook: playbooks/db_setup.yml
```

保存并退出 `nano`。现在你可以通过运行 `main.yml` Playbook 来执行你的整个工作流：

```bash
ansible-playbook main.yml -i inventory
```

输出将显示两个 Playbook 按顺序执行：

```plaintext
PLAY [Configure Web Servers] ***************************************************

TASK [Import web server setup tasks] *******************************************
imported: /home/labex/project/ansible_patterns/playbooks/../tasks/web_setup.yml

TASK [Install the httpd package] ***********************************************
ok: [web1.example.com]
ok: [web2.example.com]

TASK [Include verification tasks] **********************************************
included: /home/labex/project/ansible_patterns/playbooks/../tasks/verify_config.yml for web1.example.com, web2.example.com

TASK [Display a verification message] ******************************************
ok: [web1.example.com] => {
    "msg": "Configuration tasks applied to web1.example.com"
}
ok: [web2.example.com] => {
    "msg": "Configuration tasks applied to web2.example.com"
}

PLAY [Configure Database Servers] **********************************************

TASK [Install mariadb package] *************************************************
changed: [db1.lab.net]
changed: [db2.lab.net]

TASK [Display a confirmation message] ******************************************
ok: [db1.lab.net] => {
    "msg": "Database server db1.lab.net configured."
}
ok: [db2.lab.net] => {
    "msg": "Database server db2.lab.net configured."
}

PLAY RECAP *********************************************************************
db1.lab.net                : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
db2.lab.net                : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web1.example.com           : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2.example.com           : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

输出清楚地显示了两个独立的 Play 按顺序执行，演示了 `import_playbook` 如何有效地从较小的、可管理的部分组合成更大的工作流。
