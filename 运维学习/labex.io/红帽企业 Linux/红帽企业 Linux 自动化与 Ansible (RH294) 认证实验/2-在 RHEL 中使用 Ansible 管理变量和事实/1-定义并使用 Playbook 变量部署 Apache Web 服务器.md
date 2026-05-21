# 定义并使用 Playbook 变量部署 Apache Web 服务器

在这一步中，你将学习如何在 Ansible Playbook 中使用变量。变量对于提高自动化的灵活性、可重用性以及代码的可读性和可维护性至关重要。与其将软件包名称或文件路径等值硬编码到任务中，不如将它们定义为变量并在整个 Playbook 中引用。我们将创建一个简单的 Playbook，使用变量来安装 Apache Web 服务器 (`httpd`) 并部署一个基础网页。

1. **导航至项目目录**

    首先，确保你处于正确的工作目录中。本实验的所有工作都将在 `~/project` 目录中进行，该目录已为你创建。

    ```bash
    cd ~/project
    ```

    安装 `ansible-core` 软件包。

    ```bash
    sudo dnf install -y ansible-core
    ```

2. **创建 Ansible Playbook**

    现在，让我们创建 Playbook 文件。将其命名为 `playbook.yml`。你可以使用 `nano` 等命令行文本编辑器来创建和编辑该文件。

    ```bash
    nano playbook.yml
    ```

    该命令会在 `nano` 编辑器中打开一个空文件。现在，添加 Playbook 的初始部分。此部分定义了 Play 的名称、目标主机（由于我们在同一台机器上运行，因此为 `localhost`）以及一个 `vars` 部分，我们将在其中定义变量。

    ```yaml
    ---
    - name: Deploy Apache using variables
      hosts: localhost
      become: true
      vars:
        web_pkg: httpd
        web_content: "Hello from Ansible Variables"
    ```

    以下是 Playbook 结构的解析：

    - `hosts: localhost`：指定 Playbook 应在本地机器上运行。
    - `become: true`：告诉 Ansible 在执行任务时使用特权提升（相当于 `sudo`），这对于安装软件是必需的。
    - `vars`：这是一个字典，我们在其中定义变量的键值对。我们定义了 `web_pkg` 作为软件包名称，`web_content` 作为测试网页的内容。

3. **向 Playbook 添加任务**

    接下来，在 `vars` 部分下方，添加将使用这些变量的 `tasks`。第一个任务将安装 Apache 软件包，第二个任务将创建一个 `index.html` 文件。在 `nano` 编辑器中，将以下 `tasks` 块添加到你的 `playbook.yml` 文件中。

    ```yaml
    tasks:
      - name: Install the latest version of Apache
        ansible.builtin.dnf:
          name: "{{ web_pkg }}"
          state: latest

      - name: Create a basic index.html file
        ansible.builtin.copy:
          content: "{{ web_content }}"
          dest: /var/www/html/index.html
    ```

    注意我们是如何使用 `{{ variable_name }}` 来引用之前定义的变量的。这是 Ansible 用于变量的 Jinja2 模板语法。这使得任务定义具有通用性；如果你想安装 Nginx，只需更改 `web_pkg` 变量，而无需更改任务本身。

4. **检查并保存 Playbook**

    完整的 `playbook.yml` 文件现在应该如下所示。请仔细检查内容和缩进，因为 YAML 对空格非常敏感。

    ```yaml
    ---
    - name: Deploy Apache using variables
      hosts: localhost
      become: true
      vars:
        web_pkg: httpd
        web_content: "Hello from Ansible Variables"
      tasks:
        - name: Install the latest version of Apache
          ansible.builtin.dnf:
            name: "{{ web_pkg }}"
            state: latest

        - name: Create a basic index.html file
          ansible.builtin.copy:
            content: "{{ web_content }}"
            dest: /var/www/html/index.html
    ```

    要在 `nano` 中保存文件，请按 `Ctrl+X`，然后按 `Y` 确认更改，最后按 `Enter` 以 `playbook.yml` 为文件名保存。

5. **检查 Playbook 语法**

    在运行 Playbook 之前，检查其语法是否有错误是一个好习惯。

    ```bash
    ansible-playbook --syntax-check playbook.yml
    ```

    如果语法正确，你将看到 Playbook 的文件路径作为输出，确认其有效：

    ```plaintext
    playbook: playbook.yml
    ```

    如果看到任何错误，请使用 `nano playbook.yml` 重新打开文件并修复它们。请特别注意正确的缩进（通常为两个空格）。

6. **运行 Playbook**

    现在，执行 Playbook。Ansible 将连接到 `localhost`，读取变量并运行任务。

    ```bash
    ansible-playbook playbook.yml
    ```

    你应该会看到指示每个任务成功执行的输出。`changed` 状态意味着 Ansible 对系统进行了修改，例如安装了软件包或创建了文件。

    ```plaintext
    PLAY [Deploy Apache using variables] *******************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Install the latest version of Apache] ************************************
    changed: [localhost]

    TASK [Create a basic index.html file] ******************************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

    如果你第二次运行该 Playbook，任务应该报告 `ok` 而不是 `changed`，因为软件包已经安装且文件已经具有正确的内容。这展示了 Ansible 的幂等性（Idempotency）。

7. **手动验证配置**

    尽管 Playbook 已完成，但你可以手动验证任务是否按预期工作。首先，检查 `httpd` 软件包是否已安装：

    ```bash
    rpm -q httpd
    ```

    输出应显示软件包名称和版本：

    ```plaintext
    httpd-2.4.57-7.el9.x86_64
    ```

    接下来，检查 `index.html` 文件的内容：

    ```bash
    cat /var/www/html/index.html
    ```

    输出应与 `web_content` 变量的值匹配：

    ```plaintext
    Hello from Ansible Variables
    ```

    你已成功在 Ansible Playbook 中使用变量来配置系统。
