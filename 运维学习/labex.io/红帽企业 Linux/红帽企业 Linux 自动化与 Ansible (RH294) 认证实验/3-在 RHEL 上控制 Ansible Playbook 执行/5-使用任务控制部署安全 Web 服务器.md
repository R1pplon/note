# 使用任务控制部署安全 Web 服务器

在最后这个步骤中，你将结合你学到的所有概念——循环（loops）、条件（conditionals）、handlers 和错误处理（error handling）——来构建一个单一的、健壮的 playbook。目标是部署 Apache Web 服务器 (`httpd`)，使用 `mod_ssl` 来保护它，生成一个自签名 SSL 证书，并部署一个自定义主页。这个实践性的实验模拟了一个真实的自动化任务。

首先，让我们为这个综合实验设置项目目录。

```bash
cd ~/project
mkdir control-review-lab
cd control-review-lab
```

一如既往，创建一个 `inventory` 文件来定义你的目标宿主。

```bash
nano inventory
```

添加 `localhost` 条目：

```ini
localhost ansible_connection=local
```

保存并退出编辑器 (`Ctrl+X`, `Y`, `Enter`)。

接下来，我们需要一个目录来存储我们的 playbook 将要部署的文件。

```bash
mkdir files
```

现在，在 `files` 目录下创建一个自定义主页 `index.html`。

```bash
nano files/index.html
```

添加以下 HTML 内容。这将是我们安全 Web 服务器提供的页面。

```html
<h1>Secure Web Server Deployed by Ansible!</h1>
<p>This page is served over HTTPS.</p>
```

保存并退出编辑器。

现在是时候构建主 playbook `deploy_secure_web.yml` 了。这个 playbook 将比之前的更复杂，整合了多个概念。

```bash
nano deploy_secure_web.yml
```

输入以下完整的 playbook。阅读代码中的注释，以理解每个部分如何为整体目标做出贡献。

```yaml
---
- name: Deploy a Secure Apache Web Server
  hosts: localhost
  become: yes
  vars:
    packages_to_install:
      - httpd
      - mod_ssl
    ssl_cert_path: /etc/pki/tls/certs/localhost.crt
    ssl_key_path: /etc/pki/tls/private/localhost.key

  tasks:
    - name: Stop nginx to free port 80
      ansible.builtin.systemd:
        name: nginx
        state: stopped
      ignore_errors: yes

    - name: Install httpd and mod_ssl packages
      ansible.builtin.dnf:
        name: "{{ packages_to_install }}"
        state: present

    - name: Generate self-signed SSL certificate if it does not exist
      ansible.builtin.command: >
        openssl req -new -nodes -x509
        -subj "/C=US/ST=None/L=None/O=LabEx/CN=localhost"
        -keyout {{ ssl_key_path }}
        -out {{ ssl_cert_path }}
      args:
        creates: "{{ ssl_cert_path }}"

    - name: Deploy custom index.html
      ansible.builtin.copy:
        src: files/index.html
        dest: /var/www/html/index.html
      notify: restart httpd

    - name: Start and enable httpd service
      ansible.builtin.systemd:
        name: httpd
        state: started
        enabled: yes

  handlers:
    - name: restart httpd
      ansible.builtin.systemd:
        name: httpd
        state: restarted
```

让我们分解一下这个 playbook 的作用：

- **`vars`**: 定义要安装的软件包以及 SSL 证书和密钥的路径的变量，使 playbook 更易于阅读和维护。
- **停止 Nginx 任务**: 停止上一个实验步骤中的 nginx 服务，为 Apache 释放端口 80。如果 nginx 未运行，则使用 `ignore_errors: yes`。
- **安装任务**: 使用 `packages_to_install` 变量安装 `httpd` 和 `mod_ssl`。
- **生成证书任务**: 这是一个关键任务。它使用 `openssl` 命令创建一个自签名证书。`args: { creates: ... }` 指令使此任务具有**幂等性**。只有当证书文件 (`/etc/pki/tls/certs/localhost.crt`) 不存在时，命令才会运行。
- **部署主页任务**: 复制你的自定义 `index.html`。至关重要的是，它使用 `notify: restart httpd` 在文件更改时触发 handler。
- **启动服务任务**: 使用 systemd 模块在所有配置就绪后启动并启用 httpd 服务，确保它在启动时运行。
- **Handler**: `restart httpd` handler 使用 systemd 重新启动 Apache，这仅在配置文件或内容文件更改时触发。

保存并退出编辑器。现在，执行你的综合 playbook。

```bash
ansible-playbook -i inventory deploy_secure_web.yml
```

首次运行时，你应该会看到多个任务报告 `changed`，包括停止 nginx、安装软件包、生成证书、复制文件和启动服务。

```plaintext
...
TASK [Start and enable httpd service] ******************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=6    changed=5    unreachable=0    failed=0    ...
```

最后，验证你的安全 Web 服务器是否正常工作。首先测试 HTTP 版本，然后使用 `-k` 标志测试 HTTPS 版本，以忽略关于自签名证书的警告。

```bash
curl http://localhost
```

你应该会看到你的自定义主页的内容。

```html
<h1>Secure Web Server Deployed by Ansible!</h1>
<p>This page is served over HTTPS.</p>
```

你也可以测试 HTTPS 版本：

```bash
curl -k https://localhost
```

如果再次运行 playbook，你将看到没有任务报告 `changed`，并且 handler 未被运行，这证明了你的 playbook 是幂等的。

恭喜！你已成功构建了一个实用的、健壮的 Ansible playbook，它结合了循环、变量、幂等的命令执行和 handlers 来部署一个安全的应用。
