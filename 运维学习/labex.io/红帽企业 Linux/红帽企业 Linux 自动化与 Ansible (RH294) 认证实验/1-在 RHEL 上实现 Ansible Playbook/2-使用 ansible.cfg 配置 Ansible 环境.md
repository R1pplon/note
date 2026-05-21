# 使用 ansible.cfg 配置 Ansible 环境

在本步骤中，你将创建一个 Ansible 配置文件 `ansible.cfg`。此文件允许你设置 Ansible 的默认行为，从而避免反复在命令行中输入常用选项。通过将 `ansible.cfg` 文件放在你的项目目录中，你可以定义诸如默认 inventory 文件路径之类的设置，Ansible 在该目录运行时将自动使用这些设置。

你应该仍然处于上一步的 `~/project/ansible-lab` 目录中。

1. 使用 `nano` 文本编辑器在当前目录（`~/project/ansible-lab`）中创建一个名为 `ansible.cfg` 的新文件。

    ```bash
    nano ansible.cfg
    ```

2. 在 `nano` 编辑器中，添加以下内容。此配置告诉 Ansible 在哪里可以找到你的默认 inventory 文件。

    - `[defaults]` 部分是 `ansible.cfg` 文件的一个标准部分，你可以在其中定义大多数默认设置。
    - `inventory = ./inventory` 行将默认 inventory 设置为位于当前目录（`.`）下的 `inventory` 文件。

    ```ini
    [defaults]
    inventory = ./inventory
    ```

    通过按 `Ctrl+O`，然后按 `Enter` 保存文件，最后按 `Ctrl+X` 退出。

3. 现在你已经配置了默认 inventory 路径，只要你还在 `~/project/ansible-lab` 目录中，就不再需要对 Ansible 命令使用 `-i` 标志了。

    为了测试这一点，再次运行 `ansible-inventory --list` 命令，但这次省略 `-i inventory` 部分。

    ```bash
    ansible-inventory --list
    ```

    你应该会看到与上一步完全相同的 JSON 输出，这证实了由于新的 `ansible.cfg` 配置，Ansible 能够自动找到并使用你的 `inventory` 文件。

    ```json
    {
      "_meta": {
        "hostvars": {
          "localhost": {
            "ansible_connection": "local"
          }
        }
      },
      "all": {
        "children": ["ungrouped", "webservers"]
      },
      "webservers": {
        "hosts": ["localhost"]
      }
    }
    ```

通过创建特定于项目的 `ansible.cfg`，你已经提高了工作流程的效率。这是 Ansible 项目中常见的做法，以确保一致的行为并减少命令行复杂性。
