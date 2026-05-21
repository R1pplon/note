# 使用 LVM 和文件系统自动化存储管理

在本步骤中，你将学习如何使用 Ansible 自动化存储管理，包括创建 LVM 物理卷、卷组、逻辑卷和文件系统。这对于以一致且可扩展的方式管理基础设施中的存储资源至关重要。

你将使用诸如 `ansible.builtin.lvg` 用于卷组管理，`ansible.builtin.lvol` 用于逻辑卷创建，`ansible.builtin.filesystem` 用于文件系统创建，以及 `ansible.posix.mount` 用于挂载点管理等模块。

1. **导航到新的项目目录以进行存储管理。**

    ```bash
    cd ~/project
    mkdir system-storage
    cd system-storage
    ```

2. **创建本次实验的 inventory 文件**。

    ```bash
    cat << EOF > inventory.ini
    [webservers]
    localhost ansible_connection=local
    EOF
    ```

3. **创建一个 loop device 来模拟额外的存储**，因为我们是在虚拟环境中工作。

    ```bash
    # 创建一个 1GB 的文件用作虚拟磁盘
    sudo dd if=/dev/zero of=/tmp/virtual_disk bs=1M count=1024
    
    # 设置 loop device
    sudo losetup /dev/loop0 /tmp/virtual_disk
    
    # 验证 loop device
    lsblk | grep loop0
    ```

4. **创建一个全面的存储管理 playbook**。这将演示 LVM 操作和文件系统管理。

    ```bash
    nano storage.yml
    ```

    添加以下内容：

    ```yaml
    ---
    - name: Configure LVM storage and filesystems
      hosts: webservers
      become: true
      vars:
        storage_device: /dev/loop0
        volume_group: apache-vg
        content_lv: content-lv
        logs_lv: logs-lv
        backup_lv: backup-lv
      tasks:
        - name: Install LVM utilities
          ansible.builtin.dnf:
            name: lvm2
            state: present

        - name: Create physical volume
          community.general.lvg:
            vg: "{{ volume_group }}"
            pvs: "{{ storage_device }}"
            state: present

        - name: Create logical volume for web content
          community.general.lvol:
            vg: "{{ volume_group }}"
            lv: "{{ content_lv }}"
            size: 256m
            state: present

        - name: Create logical volume for logs
          community.general.lvol:
            vg: "{{ volume_group }}"
            lv: "{{ logs_lv }}"
            size: 256m
            state: present

        - name: Create logical volume for backup
          community.general.lvol:
            vg: "{{ volume_group }}"
            lv: "{{ backup_lv }}"
            size: 256m
            state: present

        - name: Create XFS filesystem on content volume
          community.general.filesystem:
            fstype: xfs
            dev: "/dev/{{ volume_group }}/{{ content_lv }}"
            force: no

        - name: Create XFS filesystem on logs volume
          community.general.filesystem:
            fstype: xfs
            dev: "/dev/{{ volume_group }}/{{ logs_lv }}"
            force: no

        - name: Create ext4 filesystem on backup volume
          community.general.filesystem:
            fstype: ext4
            dev: "/dev/{{ volume_group }}/{{ backup_lv }}"
            force: no

        - name: Create mount points
          ansible.builtin.file:
            path: "{{ item }}"
            state: directory
            mode: "0755"
          loop:
            - /var/www
            - /var/log/httpd
            - /backup

        - name: Mount web content volume
          ansible.posix.mount:
            path: /var/www
            src: "/dev/{{ volume_group }}/{{ content_lv }}"
            fstype: xfs
            opts: defaults
            state: mounted

        - name: Mount logs volume
          ansible.posix.mount:
            path: /var/log/httpd
            src: "/dev/{{ volume_group }}/{{ logs_lv }}"
            fstype: xfs
            opts: defaults
            state: mounted

        - name: Mount backup volume
          ansible.posix.mount:
            path: /backup
            src: "/dev/{{ volume_group }}/{{ backup_lv }}"
            fstype: ext4
            opts: defaults
            state: mounted

        - name: Set appropriate ownership for web content
          ansible.builtin.file:
            path: /var/www
            owner: apache
            group: apache
            recurse: yes

        - name: Set appropriate ownership for httpd logs
          ansible.builtin.file:
            path: /var/log/httpd
            owner: apache
            group: apache
            recurse: yes

        - name: Create html directory for web content
          ansible.builtin.file:
            path: /var/www/html
            state: directory
            owner: apache
            group: apache
            mode: "0755"

        - name: Create sample web content
          ansible.builtin.copy:
            content: |
              <html>
              <head><title>Storage Management Demo</title></head>
              <body>
              <h1>LVM Storage Configuration</h1>
              <p>This content is served from an LVM logical volume managed by Ansible.</p>
              <p>Volume Group: {{ volume_group }}</p>
              <p>Logical Volume: {{ content_lv }}</p>
              <p>Filesystem: XFS</p>
              </body>
              </html>
            dest: /var/www/html/storage.html
            owner: apache
            group: apache
            mode: "0644"
    ```

5. **安装所需的 Ansible collection** 以进行 LVM 管理。

    ```bash
    ansible-galaxy collection install community.general
    ```

6. **执行存储管理 playbook**。

    ```bash
    ansible-playbook -i inventory.ini storage.yml
    ```

    这将创建 LVM 结构、文件系统和挂载点。

7. **创建一个 playbook 来收集和显示存储信息**。

    ```bash
    nano get-storage.yml
    ```

    添加以下内容：

    ```yaml
    ---
    - name: Gather storage information
      hosts: webservers
      become: true
      tasks:
        - name: Gather disk facts
          ansible.builtin.setup:
            gather_subset:
              - hardware

        - name: Display volume group information
          ansible.builtin.command:
            cmd: vgdisplay apache-vg
          register: vg_info
          changed_when: false

        - name: Display logical volume information
          ansible.builtin.command:
            cmd: lvdisplay apache-vg
          register: lv_info
          changed_when: false

        - name: Display filesystem information
          ansible.builtin.command:
            cmd: df -h /var/www /var/log/httpd /backup
          register: fs_info
          changed_when: false

        - name: Display mount information
          ansible.builtin.command:
            cmd: cat /proc/mounts
          register: mount_info
          changed_when: false

        - name: Show volume group details
          ansible.builtin.debug:
            var: vg_info.stdout_lines

        - name: Show logical volume details
          ansible.builtin.debug:
            var: lv_info.stdout_lines

        - name: Show filesystem usage
          ansible.builtin.debug:
            var: fs_info.stdout_lines

        - name: Show fstab entries
          ansible.builtin.command:
            cmd: grep apache-vg /etc/fstab
          register: fstab_entries
          changed_when: false
          failed_when: false

        - name: Display fstab entries
          ansible.builtin.debug:
            var: fstab_entries.stdout_lines
    ```

8. **执行存储信息收集 playbook**。

    ```bash
    ansible-playbook -i inventory.ini get-storage.yml
    ```

    这将显示有关已创建存储结构的详细信息。

9. **手动验证存储配置**。

    ```bash
    # 检查 LVM 结构
    sudo vgs apache-vg
    sudo lvs apache-vg
    sudo pvs /dev/loop0
    
    # 检查文件系统
    df -h /var/www /var/log/httpd /backup
    
    # 检查挂载点
    mount | grep apache-vg
    
    # 检查 fstab 条目
    grep apache-vg /etc/fstab
    
    # 测试 Web 内容
    cat /var/www/html/storage.html
    ```

10. **创建一个存储扩展 playbook** 来演示扩展操作。

    ```bash
    nano expand_storage.yml
    ```

    添加以下内容：

    ```yaml
    ---
    - name: Expand storage volumes
      hosts: webservers
      become: true
      vars:
        volume_group: apache-vg
        content_lv: content-lv
      tasks:
        - name: Extend content logical volume
          community.general.lvol:
            vg: "{{ volume_group }}"
            lv: "{{ content_lv }}"
            size: 400m
            state: present

        - name: Extend XFS filesystem
          community.general.filesystem:
            fstype: xfs
            dev: "/dev/{{ volume_group }}/{{ content_lv }}"
            resizefs: yes

        - name: Display updated filesystem size
          ansible.builtin.command:
            cmd: df -h /var/www
          register: new_size
          changed_when: false

        - name: Show new filesystem size
          ansible.builtin.debug:
            var: new_size.stdout_lines
    ```

11. **测试存储扩展**。

    ```bash
    # 在扩展前检查当前大小
    df -h /var/www
    
    # 运行扩展 playbook
    ansible-playbook -i inventory.ini expand_storage.yml
    
    # 验证扩展
    df -h /var/www
    sudo lvs apache-vg/content-lv
    ```

你已成功使用 Ansible 自动化了 LVM 存储管理，包括物理卷创建、逻辑卷管理、文件系统创建和挂载点配置。
