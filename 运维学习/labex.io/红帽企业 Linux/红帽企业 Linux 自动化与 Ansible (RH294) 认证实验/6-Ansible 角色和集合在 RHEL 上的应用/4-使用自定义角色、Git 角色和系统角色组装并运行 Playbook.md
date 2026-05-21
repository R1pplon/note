# 使用自定义角色、Git 角色和系统角色组装并运行 Playbook

在此步骤中，你将整合所有已准备好的组件：你的自定义角色、来自 Git 的依赖以及 RHEL System Role。你将创建一个主 Playbook，用于协调这些角色，以完全配置开发 Web 服务器。

将此步骤想象成从不同零件组装一台复杂的机器——每个角色都有特定的用途，它们协同工作以创建完整的 Web 服务器环境。让我们将此分解为易于管理的部分：

首先，确保你位于主项目目录中。

```bash
cd ~/project
```

在深入配置之前，让我们了解一下我们正在创建的内容：

- **Ansible 配置 (Ansible Configuration)**：设置 Ansible 的行为方式及其文件查找位置
- **清单 (Inventory)**：定义要管理的服务器（在本例中为 localhost）
- **变量 (Variables)**：存储角色将使用的数据（开发者信息、SELinux 设置）
- **自定义角色内容 (Custom Role Content)**：配置开发者环境的实际任务
- **主 Playbook (Main Playbook)**：按正确顺序运行所有内容的协调器

### 1. 创建 Ansible 配置和清单

`ansible.cfg` 文件就像一个配置文件，它告诉 Ansible 如何运行。没有它，你需要在每个命令中指定路径和选项。有了它，Ansible 会自动知道在哪里找到你的角色、集合和清单。

使用 `nano` 创建 `ansible.cfg` 文件。此文件告诉 Ansible 在哪里查找你的角色、集合和清单。

```bash
nano ansible.cfg
```

添加以下内容。让我们了解每一行：

```ini
[defaults]
inventory = inventory
roles_path = roles
collections_paths = collections
host_key_checking = False

[privilege_escalation]
become = True
```

**每个设置的作用：**

- `inventory = inventory`：无需每次都输入 `-i inventory`，Ansible 将自动使用此文件
- `roles_path = roles`：Ansible 将在 `roles` 目录中查找角色
- `collections_paths = collections`：Ansible 将在此处找到你安装的集合
- `host_key_checking = False`：在实验 (lab) 环境中防止 SSH 密钥验证错误
- `become = True`：在需要时自动以提升的权限运行任务

保存并退出 `nano`（按 `Ctrl+X`，然后按 `Y`，然后按 `Enter`）。

清单文件告诉 Ansible 要管理哪些机器。在本例中，我们正在配置本地机器。

```bash
nano inventory
```

添加以下行：

```ini
localhost ansible_connection=local
```

**这意味着：**

- `localhost`：我们目标主机的名称
- `ansible_connection=local`：不使用 SSH，而是使用本地连接（因为我们正在管理运行 Ansible 的同一台机器）

保存并退出 `nano`。

### 2. 定义角色变量

Ansible 中的变量就像你的角色可以使用的设置。你无需在任务中硬编码用户名或端口号等值，而是在变量文件中定义它们。这使得你的自动化具有灵活性和可重用性。

`group_vars/all` 目录是一个特殊位置，Ansible 会自动加载适用于所有主机的变量。此目录中的任何 YAML 文件都可供你的 Playbook 和角色使用。

创建适用于所有主机的变量目录结构：

```bash
mkdir -p group_vars/all
```

现在，创建一个文件来定义开发者信息。此数据将由你的自定义角色用于创建用户帐户和 Web 配置。

```bash
nano group_vars/all/developers.yml
```

添加以下内容：

```yaml
---
web_developers:
  - username: jdoe # 第一位开发者
    port: 9081 # 此开发者网站的自定义端口
  - username: jdoe2 # 第二位开发者
    port: 9082 # 此开发者网站的自定义端口
```

**此数据结构意味着：**

- `web_developers`：一个包含开发者信息的列表
- 每位开发者都有一个 `username` 和一个 `port`
- 你的自定义角色将遍历此列表以创建每个开发者的配置

保存并退出。

接下来，为 SELinux 配置创建一个变量文件。SELinux (Security-Enhanced Linux) 是一个控制应用程序可以执行操作的安全模块。

```bash
nano group_vars/all/selinux.yml
```

添加以下内容：

```yaml
---
selinux_state: enforcing # 将 SELinux 设置为 enforcing 模式（最高安全性）
selinux_ports: # 允许 Apache 使用的端口列表
  - ports: "9081" # 允许端口 9081
    proto: "tcp" # 协议：TCP
    setype: "http_port_t" # SELinux 类型：HTTP 端口
    state: "present" # 添加此规则
  - ports: "9082" # 允许端口 9082
    proto: "tcp" # 协议：TCP
    setype: "http_port_t" # SELinux 类型：HTTP 端口
    state: "present" # 添加此规则
```

**理解 SELinux 设置：**

- `selinux_state: enforcing`：SELinux 将主动阻止未经授权的操作
- `selinux_ports`：端口配置列表
- `http_port_t`：允许 Apache 绑定到端口的 SELinux 类型
- 默认情况下，Apache 只能使用端口 80 和 443；我们需要显式允许 9081 和 9082

保存并退出。

### 3. 填充自定义角色

你的 `apache.developer_configs` 角色目前只有目录结构，但没有实际内容。我们需要添加：

- **模板 (Templates)**：可以包含变量的文件（使用 Jinja2 语法）
- **任务 (Tasks)**：Ansible 将执行的实际工作
- **处理程序 (Handlers)**：仅在被通知时运行的特殊任务（例如重启服务）
- **元数据 (Metadata)**：关于角色依赖项的信息

模板允许你创建基于变量进行调整的配置文件。`.j2` 扩展名表示这是一个 Jinja2 模板。

```bash
nano roles/apache.developer_configs/templates/developer.conf.j2
```

添加以下内容：

```jinja
{% for dev in web_developers %}
Listen {{ dev.port }}
<VirtualHost *:{{ dev.port }}>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/{{ dev.username }}

    <Directory /var/www/{{ dev.username }}>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
{% endfor %}
```

**理解模板语法：**

- `{% for dev in web_developers %}`：开始遍历开发者列表的循环
- `{{ dev.port }}`：插入此开发者的端口号
- `{{ dev.username }}`：插入此开发者的用户名
- `{% endfor %}`：结束循环
- 结果将是为每位开发者生成的独立虚拟主机配置

**这会创建什么：** 对于我们的两位开发者，此模板将生成 Apache 配置，该配置：

1. 使 Apache 监听端口 9081 和 9082
2. 创建虚拟主机，从 `/var/www/jdoe` 和 `/var/www/jdoe2` 提供内容
3. 为每个目录设置适当的权限

保存并退出。

任务是 Ansible 执行的实际工作。每个任务都使用 Ansible 模块来完成特定操作。

```bash
nano roles/apache.developer_configs/tasks/main.yml
```

添加以下内容，让我们了解每个任务：

```yaml
---
# 任务 1：为每位开发者创建用户帐户
- name: Create developer user accounts
  ansible.builtin.user: # 使用 'user' 模块
    name: "{{ item.username }}" # 创建具有此名称的用户
    state: present # 确保用户存在
  loop: "{{ web_developers }}" # 对列表中的每位开发者执行此操作

# 任务 2：为每位开发者创建 Web 目录
- name: Create developer web root directories
  ansible.builtin.file: # 使用 'file' 模块
    path: "/var/www/{{ item.username }}" # 创建此目录
    state: directory # 确保它是一个目录
    owner: "{{ item.username }}" # 设置所有者
    group: "{{ item.username }}" # 设置组
    mode: "0755" # 设置权限 (rwxr-xr-x)
  loop: "{{ web_developers }}"

# 任务 3：为每位开发者创建示例网页
- name: Create a sample index.html for each developer
  ansible.builtin.copy: # 使用 'copy' 模块
    content: "Welcome to {{ item.username }}'s dev space\n" # 文件内容
    dest: "/var/www/{{ item.username }}/index.html" # 文件存放位置
    owner: "{{ item.username }}" # 文件所有者
    group: "{{ item.username }}" # 文件组
    mode: "0644" # 文件权限 (rw-r--r--)
  loop: "{{ web_developers }}"

# 任务 4：部署 Apache 配置文件
- name: Deploy developer apache configs
  ansible.builtin.template: # 使用 'template' 模块
    src: developer.conf.j2 # 源模板文件
    dest: /etc/httpd/conf.d/developer.conf # 服务器上的目标位置
    mode: "0644" # 文件权限
  notify: restart apache # 更改时触发 restart apache 处理程序
```

**理解关键概念：**

- `loop`：为列表中的每个项目重复任务
- `{{ item.username }}`：在循环中引用当前项目的用户名
- `notify: restart apache`：当此任务进行更改时，它将触发一个名为“restart apache”的处理程序
- 文件权限：`0755` 表示所有者可读/写/执行，其他人可读/执行；`0644` 表示所有者可读/写，其他人只能读

保存并退出。

处理程序是仅在被其他任务通知时运行的特殊任务。它们通常用于重启服务等操作。

```bash
nano roles/apache.developer_configs/handlers/main.yml
```

添加以下内容：

```yaml
---
- name: restart apache # 此名称必须与 notify: 语句匹配
  ansible.builtin.service: # 使用 'service' 模块
    name: httpd # 服务名称（在 RHEL 上，Apache 称为 'httpd'）
    state: restarted # 重启服务
```

**为什么使用处理程序？**

- 效率：仅当配置实际更改时才重启服务
- 顺序：所有任务先运行，然后所有处理程序在最后运行
- 幂等性：多个任务可以通知同一个处理程序，但它只运行一次

保存并退出。

最后，我们需要告诉 Ansible 我们的自定义角色依赖于我们之前安装的 `infra.apache` 角色。

```bash
nano roles/apache.developer_configs/meta/main.yml
```

将文件内容替换为：

```yaml
---
dependencies:
  - role: infra.apache # 此角色必须在我们的自定义角色之前运行
```

**这会做什么：**

- 当 Ansible 运行 `apache.developer_configs` 时，它将首先自动运行 `infra.apache`
- 这确保在添加自定义配置之前已安装并配置好 Apache
- 依赖项按列出的顺序运行

保存并退出。

### 4. 组装并运行主 Playbook

Playbook 就像一个食谱，告诉 Ansible 要做什么以及按什么顺序执行。我们的 Playbook 将：

1. 配置 SELinux 设置（`pre_tasks`）
2. 运行我们的角色（包括依赖链）

创建主 Playbook 文件：

```bash
nano web_dev_server.yml
```

添加以下内容并附带详细说明：

```yaml
---
- name: Configure Dev Web Server # Playbook 名称
  hosts: localhost # 在 localhost 上运行
  pre_tasks: # 在角色之前运行的任务
    # 任务 1：配置 SELinux 模式
    - name: Set SELinux to enforcing mode
      ansible.posix.selinux: # 来自 ansible.posix 集合的模块
        policy: targeted # 使用 'targeted' SELinux 策略
        state: "{{ selinux_state }}" # 使用我们定义的变量
      when: selinux_state is defined # 仅当变量存在时运行

    # 任务 2：配置 SELinux 端口
    - name: Configure SELinux ports for Apache
      community.general.seport: # 来自 community.general 集合的模块
        ports: "{{ item.ports }}" # 端口号
        proto: "{{ item.proto }}" # 协议 (tcp)
        setype: "{{ item.setype }}" # SELinux 类型 (http_port_t)
        state: "{{ item.state }}" # present 或 absent
      loop: "{{ selinux_ports }}" # 遍历我们的端口列表
      when: selinux_ports is defined # 仅当变量存在时运行

  roles: # 要执行的角色
    - apache.developer_configs # 我们的自定义角色（它将触发 infra.apache）
```

**理解执行顺序：**

1. `pre_tasks`：SELinux 配置首先运行
2. `roles`：角色依赖项运行（`infra.apache`），然后是我们的自定义角色
3. `handlers`：任何被通知的处理程序最后运行

**为什么这个顺序很重要：**

- 在 Apache 尝试绑定到自定义端口之前必须配置 SELinux
- 在配置虚拟主机之前必须安装 Apache
- 服务重启发生在所有配置完成后

保存并退出。

现在你已准备好执行完整的自动化：

```bash
ansible-playbook web_dev_server.yml
```

Playbook 将执行，你将看到详细的输出。以下是预期结果（例如）：

```plaintext
PLAY [Configure Dev Web Server] *************************************************

TASK [Gathering Facts] **********************************************************
ok: [localhost]                     # Ansible 收集系统信息

TASK [Set SELinux to enforcing mode] *******************************************
changed: [localhost]                # SELinux 模式已更改

TASK [Configure SELinux ports for Apache] **************************************
changed: [localhost] => (item={'ports': '9081', 'proto': 'tcp', 'setype': 'http_port_t', 'state': 'present'})
changed: [localhost] => (item={'ports': '9082', 'proto': 'tcp', 'setype': 'http_port_t', 'state': 'present'})

TASK [infra.apache : Ensure Apache is installed.] *******************************
changed: [localhost]                # Apache 包已安装

TASK [apache.developer_configs : Create developer user accounts] ****************
changed: [localhost] => (item={'username': 'jdoe', 'port': 9081})
changed: [localhost] => (item={'username': 'jdoe2', 'port': 9082})

TASK [apache.developer_configs : Create developer web root directories] *********
changed: [localhost] => (item={'username': 'jdoe', 'port': 9081})
changed: [localhost] => (item={'username': 'jdoe2', 'port': 9082})

TASK [apache.developer_configs : Create a sample index.html for each developer] *
changed: [localhost] => (item={'username': 'jdoe', 'port': 9081})
changed: [localhost] => (item={'username': 'jdoe2', 'port': 9082})

TASK [apache.developer_configs : Deploy developer apache configs] ***************
changed: [localhost]                # 配置文件已创建

RUNNING HANDLER [apache.developer_configs : restart apache] *********************
changed: [localhost]                # Apache 已重启

PLAY RECAP **********************************************************************
localhost                  : ok=17   changed=12   unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
```

你已成功组装并运行了一个复杂的 Playbook，该 Playbook 结合了来自不同来源的多个角色，以创建完整的 Web 开发环境！
