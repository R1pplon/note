# 使用来自受管主机的自定义事实配置 Web 服务器

在这一步中，你将学习如何使用自定义事实。虽然 Ansible 会自动收集广泛的标准事实，但你也可以定义自己的事实。这些被称为“本地事实”或“自定义事实”。这是一个强大的功能，允许你将受管主机上的特定信息（例如 Ansible 默认不收集的应用程序设置或硬件特定数据）提供给 Playbook。

Ansible 会在受管主机上的 `/etc/ansible/facts.d` 目录中查找自定义事实。该目录中任何带有 `.fact` 扩展名的文件都将被处理。这些文件可以是简单的 INI 风格文本文件或 JSON 文件。

1. **创建自定义事实目录**

    首先，你需要创建 Ansible 查找自定义事实文件的目录。由于这是一个系统目录，你必须使用 `sudo` 来创建它。

    ```bash
    sudo mkdir -p /etc/ansible/facts.d
    ```

    `-p` 标志确保如果目录已存在，命令不会返回错误。

2. **创建自定义事实文件**

    现在，让我们创建一个自定义事实文件，为我们的 Web 服务器定义一条欢迎消息。我们将在 `/etc/ansible/facts.d` 目录中创建一个名为 `web_config.fact` 的 INI 格式文件。

    ```bash
    sudo nano /etc/ansible/facts.d/web_config.fact
    ```

    向文件中添加以下内容。这定义了一个包含键 `welcome_message` 的 `[webserver]` 部分。

    ```ini
    [webserver]
    welcome_message = Welcome to the server configured by Custom Facts!
    ```

    保存文件并按 `Ctrl+X`、`Y` 和 `Enter` 退出 `nano`。

3. **创建使用自定义事实的 Playbook**

    有了自定义事实，我们现在可以创建一个读取该事实并将其用于配置 Web 服务器主页的 Playbook。在你的 `~/project` 目录中，创建一个名为 `configure_web.yml` 的新 Playbook。

    ```bash
    cd ~/project
    nano configure_web.yml
    ```

    向 Playbook 添加以下内容。此 Playbook 将使用我们自定义事实中定义的消息更新 `/var/www/html/index.html` 文件。

    ```yaml
    ---
    - name: Configure web server using custom facts
      hosts: localhost
      become: true
      tasks:
        - name: Update index.html with custom message
          ansible.builtin.copy:
            content: "{{ ansible_facts.ansible_local.web_config.webserver.welcome_message }}"
            dest: /var/www/html/index.html
    ```

    让我们解析一下变量 `{{ ansible_facts.ansible_local.web_config.webserver.welcome_message }}`：

    - `ansible_facts`：所有事实的根字典。
    - `ansible_local`：存储所有自定义事实的键。
    - `web_config`：我们的事实文件名（`web_config.fact`），不带扩展名。
    - `webserver`：INI 文件中的部分名称 `[webserver]`。
    - `welcome_message`：我们要使用的值的键。

    保存文件并退出 `nano`。

4. **运行配置 Playbook**

    现在，执行 Playbook 以应用配置。

    ```bash
    ansible-playbook configure_web.yml
    ```

    输出应显示 `copy` 任务已 `changed`（更改）了 `index.html` 文件。

    ```plaintext
    PLAY [Configure web server using custom facts] *********************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Update index.html with custom message] ***********************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

5. **验证结果**

    最后，让我们验证网页是否已正确更新。使用 `cat` 命令查看 `index.html` 文件的内容。

    ```bash
    cat /var/www/html/index.html
    ```

    输出现在应显示来自你的自定义事实文件的消息：

    ```plaintext
    Welcome to the server configured by Custom Facts!
    ```

    你已成功在受管主机上创建了自定义事实，并在 Playbook 中使用它来动态配置服务。这种技术对于使你的自动化更加灵活和数据驱动非常有用。
