# 实现第二个 Play 来测试 Web 服务器部署

在最后这个步骤中，你将为你的 Playbook 添加第二个 Play。一个 Playbook 文件可以包含多个 Play，它们会按顺序执行。这对于组织针对不同主机或具有不同目的的任务非常有用。你将添加一个新 Play，该 Play 只在控制节点（`localhost`）上运行，以测试在第一个 Play 中配置的 Web 服务器。

你应该仍然处于 `~/project/ansible-lab` 目录中。

1. 你现在将为你的 Playbook 添加第二个 Play。为确保在添加第二个 Play 时具有正确的 YAML 格式，建议用下面显示的完整双 Play 内容重新创建该文件。

    **重要提示**：为避免在添加第二个 Play 时出现 YAML 缩进错误，请删除现有的 `apache.yml` 文件，并用下面显示的完整双 Play 内容创建一个新文件。

    ```bash
    rm apache.yml
    nano apache.yml
    ```

2. 你将向 Playbook 添加第二个 Play。第二个 Play 允许你组织针对不同主机或具有不同目的的任务。

    - `name: Test web server`: 为新 Play 提供一个描述性名称。
    - `hosts: localhost`: 此 Play 将在 `localhost`（即控制节点本身）上运行。
    - `become: false`: 此测试不需要 root 权限，因此我们显式禁用了权限提升。
    - **任务：验证 Web 内容。** 此任务使用 `ansible.builtin.uri` 模块向 Web 服务器发出 HTTP 请求。它检查服务器是否返回状态码 200（OK），并且返回的内容是否包含字符串 "Ansible-managed"。这自动化了你一直在手动进行的 `curl` 和 `grep` 检查。

3. 复制并粘贴下面完整的 `apache.yml` Playbook 内容，该内容现在包含两个 Play：

    ```yaml
    ---
    - name: Install and start Apache web server
      hosts: webservers
      become: true
      tasks:
        - name: Install httpd package
          ansible.builtin.dnf:
            name: httpd
            state: present

        - name: Deploy custom index.html
          ansible.builtin.copy:
            src: index.html
            dest: /var/www/html/index.html

        - name: Start and enable httpd service
          ansible.builtin.service:
            name: httpd
            state: started
            enabled: true

    - name: Test web server from localhost
      hosts: localhost
      become: false
      tasks:
        - name: Verify web server is serving correct content
          ansible.builtin.uri:
            url: http://localhost
            return_content: yes
            status_code: 200
          register: result
          failed_when: "'Ansible-managed' not in result.content"
    ```

    保存文件并退出 `nano`（`Ctrl+O`，`Enter`，`Ctrl+X`）。

4. 运行完整的 Playbook。Ansible 将执行第一个 Play，发现所有任务都已处于其目标状态（`ok`），然后继续执行第二个 Play 来运行测试。

    ```bash
    ansible-playbook apache.yml
    ```

    输出将显示两个 Play 的执行情况。所有任务都应成功完成，状态为 `ok`。

    ```plaintext
    PLAY [Install and start Apache web server] *************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Install httpd package] ***************************************************
    ok: [localhost]

    TASK [Deploy custom index.html] ************************************************
    ok: [localhost]

    TASK [Start and enable httpd service] ******************************************
    ok: [localhost]

    PLAY [Test web server from localhost] ******************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Verify web server is serving correct content] ****************************
    ok: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=6    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

通过添加第二个 Play，你创建了一个更健壮的自动化工作流，该工作流不仅配置了服务，还包含了一个内置测试来验证部署是否成功。
