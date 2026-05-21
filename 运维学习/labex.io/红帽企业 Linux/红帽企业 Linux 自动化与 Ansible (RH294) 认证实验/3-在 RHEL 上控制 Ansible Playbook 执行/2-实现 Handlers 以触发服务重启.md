# 实现 Handlers 以触发服务重启

在本步骤中，你将了解 Ansible handlers。Handlers 是特殊的任务，仅在被其他任务“通知”（notified）时运行。它们通常用于仅在发生更改时才执行的操作，例如在更新配置文件后重新启动服务。这种方法比每次 playbook 运行时都重新启动服务更有效，因为它确保了操作仅在必要时才执行。

我们将构建一个 playbook，它将安装 Nginx Web 服务器，部署自定义主页，并使用 handler 在主页内容更改时重新加载 Nginx。

首先，让我们为本次实验创建一个新目录，以保持项目整洁。

```bash
cd ~/project
mkdir control-handlers-lab
cd control-handlers-lab
```

和之前一样，我们需要一个清单文件来告诉 Ansible 在哪里运行 playbook。

```bash
nano inventory
```

添加以下行以指定本地机器。

```ini
localhost ansible_connection=local
```

保存并退出编辑器 (`Ctrl+X`, `Y`, `Enter`)。

接下来，我们需要一个文件作为我们 Web 服务器的主页。我们将创建一个 `files` 目录来存放它。

```bash
mkdir files
```

现在，在 `files` 目录中创建一个简单的 `index.html` 文件。

```bash
nano files/index.html
```

添加以下 HTML 内容：

```html
<h1>Welcome to the Ansible Handler Lab!</h1>
```

保存并退出编辑器。

现在，你将创建 playbook `deploy_nginx.yml`。此 playbook 将执行三个主要操作：安装 Nginx，复制 `index.html` 文件，并定义一个重新加载 Nginx 的 handler。

```bash
nano deploy_nginx.yml
```

输入以下内容。请密切关注“Copy homepage”任务中的 `notify` 关键字以及末尾相应的 `handlers` 部分。`become: yes` 指令告诉 Ansible 使用 `sudo` 权限执行任务，这对于安装软件包和管理服务是必需的。

```yaml
---
- name: Deploy Nginx with a handler
  hosts: localhost
  become: yes
  tasks:
    - name: Ensure Nginx is installed
      ansible.builtin.dnf:
        name: nginx
        state: present

    - name: Start and enable Nginx service
      ansible.builtin.systemd:
        name: nginx
        state: started
        enabled: yes

    - name: Copy homepage
      ansible.builtin.copy:
        src: files/index.html
        dest: /usr/share/nginx/html/index.html
      notify: reload nginx

  handlers:
    - name: reload nginx
      ansible.builtin.systemd:
        name: nginx
        state: reloaded
```

保存并退出编辑器。

现在，第一次运行 playbook。

```bash
ansible-playbook -i inventory deploy_nginx.yml
```

你将看到输出显示 Nginx 已安装（或已存在），Nginx 服务已启动并启用，`index.html` 文件已复制（状态为 `changed`），最重要的是，handler 在 play 结束时被通知并执行。

```plaintext
...
TASK [Copy homepage] ***********************************************************
changed: [localhost]

RUNNING HANDLER [reload nginx] *************************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

你可以使用 `curl` 来验证 Web 服务器是否正在运行并提供你的自定义页面。

```bash
curl http://localhost
```

输出应该是你的 `index.html` 文件内容。

```html
<h1>Welcome to the Ansible Handler Lab!</h1>
```

现在，再次运行完全相同的 playbook，但不做任何更改。

```bash
ansible-playbook -i inventory deploy_nginx.yml
```

这次，请观察输出。由于文件在目标上已与源匹配，“Copy homepage”任务将报告 `ok` 而不是 `changed`。“Start and enable Nginx service”任务也将报告 `ok`，因为服务已在运行并启用。由于没有任务通知 handler，因此 handler 未运行。

```plaintext
...
TASK [Copy homepage] ***********************************************************
ok: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

为了再次看到 handler 的作用，让我们修改源 `index.html` 文件。

```bash
nano files/index.html
```

将内容更改为以下内容：

```html
<h1>The Handler Ran Again!</h1>
```

保存并退出。现在，再运行一次 playbook。

```bash
ansible-playbook -i inventory deploy_nginx.yml
```

由于源文件已更改，“Copy homepage”任务将再次报告 `changed`，这会触发并运行 `reload nginx` handler。

```plaintext
...
TASK [Copy homepage] ***********************************************************
changed: [localhost]

RUNNING HANDLER [reload nginx] *************************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

最后一次使用 `curl` 验证更改。

```bash
curl http://localhost
```

你应该会看到更新后的消息。

```html
<h1>The Handler Ran Again!</h1>
```

本次实验展示了 handlers 在响应配置更改时管理服务状态的强大功能和效率。
