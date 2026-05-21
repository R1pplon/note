# 使用 `stat` 验证文件状态并使用 `fetch` 检索日志

在本步骤中，你将学习两个重要的数据收集模块：`ansible.builtin.stat` 和 `ansible.builtin.fetch`。`stat` 模块用于检查托管主机上文件或目录的状态——例如，查看它是否存在、其权限是什么，或者最后修改时间。它不会更改任何内容，因此非常适合用于检查和条件逻辑。`fetch` 模块的作用与 `copy` 相反：它从托管主机检索文件，并将它们保存在你的控制节点上，这对于备份配置或收集日志文件进行分析非常理想。

我们将创建一个 playbook，该 playbook 首先检查你之前创建的 `/etc/motd` 文件是否存在，然后将 DNF 包管理器日志文件 (`/var/log/dnf.log`) 检索到你的 LabEx VM 上的本地目录。

1. **首先，确保你在 `~/project` 目录中，并创建一个新的子目录来存储你将要检索的文件。**

    ```bash
    cd ~/project
    mkdir fetched_logs
    ```

2. **创建一个名为 `check_and_fetch.yml` 的新 playbook**。此 playbook 将包含检查文件和检索日志的任务。

    ```bash
    nano ~/project/check_and_fetch.yml
    ```

3. **将以下内容添加到你的 `check_and_fetch.yml` playbook 中。** 此 playbook 使用 `stat` 获取文件详细信息，使用 `register` 将这些详细信息存储在变量中，使用 `debug` 显示变量，并使用 `fetch` 检索日志文件。

    ```yaml
    ---
    - name: Check file status and fetch logs
      hosts: localhost
      become: true
      tasks:
        - name: Check if /etc/motd exists
          ansible.builtin.stat:
            path: /etc/motd
          register: motd_status

        - name: Display stat results
          ansible.builtin.debug:
            var: motd_status.stat

        - name: Fetch the dnf log file from managed host
          ansible.builtin.fetch:
            src: /var/log/dnf.log
            dest: fetched_logs/
            flat: yes
    ```

    让我们分解一下关键概念：

    - `register: motd_status`: 这是 Ansible 的一个关键功能。它获取任务的整个输出，并将其保存到一个名为 `motd_status` 的新变量中。
    - `ansible.builtin.debug`: 此模块用于在 playbook 运行期间打印值。在这里，我们打印了已注册变量 (`motd_status.stat`) 中的 `stat` 对象，以查看文件的属性。
    - `ansible.builtin.fetch`: 此模块从托管主机检索文件。
      - `src`: 要从托管主机检索的文件的路径。
      - `dest`: 控制节点（你的 LabEx VM）上保存文件的目录。
      - `flat: yes`: 默认情况下，`fetch` 会创建与主机和源路径匹配的子目录结构。`flat: yes` 通过将文件直接复制到 `dest` 目录而不添加任何额外的子目录来简化此过程。

4. **执行 playbook。** 由于我们要读取系统日志文件，因此使用 `become: true` 来获取必要的权限。

    ```bash
    ansible-playbook -i inventory.ini check_and_fetch.yml
    ```

    输出将显示 debug 任务中 `stat` 检查的结果，然后是 `fetch` 任务。

    ```plaintext
    PLAY [Check file status and fetch logs] ****************************************

    TASK [Gathering Facts] *********************************************************
    ok: [localhost]

    TASK [Check if /etc/motd exists] ***********************************************
    ok: [localhost]

    TASK [Display stat results] ****************************************************
    ok: [localhost] => {
        "motd_status.stat": {
            "exists": true,
            "gid": 0,
            "isreg": true,
            "mode": "0644",
            "path": "/etc/motd",
            ...
        }
    }

    TASK [Fetch the dnf log file from managed host] ********************************
    changed: [localhost]

    PLAY RECAP *********************************************************************
    localhost                  : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

5. **验证日志文件是否已成功检索。** 列出 `fetched_logs` 目录的内容。

    ```bash
    ls -l ~/project/fetched_logs/
    ```

    你应该会看到 `dnf.log` 文件，现在它已本地存储在你的控制节点上。

    ```plaintext
    total 4
    -rw-r--r--. 1 labex labex 1234 Jul 10 15:30 dnf.log
    ```

你现在已经学会了如何在不进行更改的情况下检查文件属性，以及如何将托管系统中的重要文件检索回你的控制节点。
