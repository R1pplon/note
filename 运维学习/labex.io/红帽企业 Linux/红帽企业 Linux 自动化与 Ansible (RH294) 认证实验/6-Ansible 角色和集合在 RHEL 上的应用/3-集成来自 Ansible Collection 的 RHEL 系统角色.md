# 集成来自 Ansible Collection 的 RHEL 系统角色

在此步骤中，你将使用 Ansible Collections，这是分发 Ansible 内容的标准方式，包括角色、模块和插件。你将安装 Community General collection，它提供了一组有用的模块，用于自动化常见的管理任务，包括 SELinux 管理。

对于我们的 Web 服务器场景，我们需要正确配置 SELinux，以允许 Apache 服务监听非标准端口。`community.general` collection 包含非常适合此任务的 SELinux 模块。

首先，确保你位于主项目目录中。

```bash
cd ~/project
```

将 collections 安装在项目目录内是一种最佳实践，这可以使你的项目自包含。创建一个名为 `collections` 的目录来存储它们。

```bash
mkdir collections
```

现在，使用 `ansible-galaxy collection install` 命令安装所需的 collections。`-p` 标志指示命令将 collections 安装到你刚刚创建的 `collections` 目录中。

```bash
ansible-galaxy collection install community.general:7.5.0 ansible.posix:1.5.4 -p collections
```

该命令将下载 collections 及其依赖项。你将看到类似以下的输出：

```plaintext
Starting galaxy collection install process
Process install dependency map
Starting collection install process
Installing 'community.general:7.5.0' to '/home/labex/project/collections/ansible_collections/community/general'
Installing 'ansible.posix:1.5.4' to '/home/labex/project/collections/ansible_collections/ansible/posix'
...
community.general:7.5.0 was installed successfully
ansible.posix:1.5.4 was installed successfully
```

要验证 collection 是否已对你的项目可用，你可以通过指定 collections 路径来列出所有已安装的 collections。

```bash
ansible-galaxy collection list -p collections
```

输出将显示已安装的 collections 及其在项目内的安装路径。

```plaintext
# /home/labex/project/collections/ansible_collections
Collection              Version
----------------------- -------
ansible.posix           1.5.4
community.general       7.5.0
```

在 playbook 中使用 collection 中的模块时，必须通过其完全限定的 collection 名称 (FQCN) 来引用它。对于 SELinux 管理，你将使用 `ansible.posix.selinux` 来管理 SELinux 状态，使用 `community.general.seport` 来管理 SELinux 端口。

你现在已成功安装了包含 SELinux 管理模块的强大 collections。在下一步中，你将组合一个 playbook，该 playbook 使用你的自定义角色、来自 Git 的角色以及来自这些 collections 的 SELinux 模块来完全配置开发 Web 服务器。
