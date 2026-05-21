# 使用 `include_tasks` 和 `import_tasks` 模块化 Play

在本步骤中，你将学习如何通过将大型 Ansible 项目分解为更小、可重用的文件来构建它们。随着 Playbook 的增长，将所有任务保存在单个文件中会变得难以管理。Ansible 为此提供了两个主要指令：`import_tasks` 和 `include_tasks`。两者都允许你从另一个文件中引入任务。

- `import_tasks` 是**静态的**。它在 Playbook 被 Ansible 首次解析时处理。这最适合 Play 中无条件、结构化的部分。
- `include_tasks` 是**动态的**。它在 Play 执行期间处理。这使其适用于循环和条件语句。

我们现在将重构我们的 Playbook 以同时使用它们。首先，请确保你位于项目目录中：

```bash
cd ~/project/ansible_patterns
```

在继续之前，让我们更新 inventory 文件，使主机指向 localhost 以便在此实验环境中使用。这将允许 Playbook 成功运行：

```bash
nano inventory
```

用以下配置替换内容，该配置将示例主机映射到 localhost：

```ini
[webservers]
web1.example.com ansible_host=localhost ansible_connection=local
web2.example.com ansible_host=localhost ansible_connection=local

[dbservers]
db1.lab.net ansible_host=localhost ansible_connection=local
db2.lab.net ansible_host=localhost ansible_connection=local
```

保存并退出编辑器。此配置使用 `ansible_host=localhost` 将连接重定向到本地机器，并使用 `ansible_connection=local` 来避免 SSH 连接尝试。

一种常见的做法是将可重用的任务文件存储在专用的子目录中。让我们创建一个名为 `tasks` 的目录：

```bash
mkdir tasks
```

现在，让我们为可能适用于许多服务器的通用设置任务创建一个文件。我们将在此处放置一个安装 `httpd` Web 服务器包的任务：

```bash
nano tasks/web_setup.yml
```

添加以下内容。请注意，此文件只是一个任务列表；它不包含完整的 Play 结构（如 `hosts:` 或 `name:`）：

```yaml
- name: Install the httpd package
  ansible.builtin.dnf:
    name: httpd
    state: present
  become: true
```

保存并退出 `nano`。接下来，创建第二个任务文件用于简单的验证步骤：

```bash
nano tasks/verify_config.yml
```

将此调试任务添加到此文件中：

```yaml
- name: Display a verification message
  ansible.builtin.debug:
    msg: "Configuration tasks applied to {{ inventory_hostname }}"
```

保存并退出编辑器。现在，让我们修改主 `playbook.yml` 文件以使用这些新的任务文件。我们将使用 `import_tasks` 进行静态设置，使用 `include_tasks` 进行动态验证消息：

```bash
nano playbook.yml
```

用以下内容替换 `playbook.yml` 的全部内容。此 Playbook 现在将目标设置为 `webservers` 组，并使用模块化任务文件：

```yaml
---
- name: Configure Web Servers
  hosts: webservers
  gather_facts: false
  tasks:
    - name: Import web server setup tasks
      import_tasks: tasks/web_setup.yml

    - name: Include verification tasks
      include_tasks: tasks/verify_config.yml
```

保存文件并运行 Playbook：

```bash
ansible-playbook playbook.yml -i inventory
```

你应该会看到模块化任务正在执行：

```plaintext
PLAY [Configure Web Servers] ***************************************************

TASK [Import web server setup tasks] *******************************************
imported: /home/labex/project/ansible_patterns/tasks/web_setup.yml

TASK [Install the httpd package] ***********************************************
changed: [web1.example.com]
changed: [web2.example.com]

TASK [Include verification tasks] **********************************************
included: /home/labex/project/ansible_patterns/tasks/verify_config.yml for web1.example.com, web2.example.com

TASK [Display a verification message] ******************************************
ok: [web1.example.com] => {
    "msg": "Configuration tasks applied to web1.example.com"
}
ok: [web2.example.com] => {
    "msg": "Configuration tasks applied to web2.example.com"
}

PLAY RECAP *********************************************************************
web1.example.com           : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2.example.com           : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

请注意输出如何清晰地指示何时从各自的文件导入和包含任务。这种模块化方法使你的自动化更简洁，更易于维护。
