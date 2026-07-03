# 在 build 任务中添加 runs-on 键并设置为 ubuntu-latest

在这一步中，你将克隆仓库，并更新一个现有的工作流文件，以定义一个任务（job）并指定它应该在哪个操作系统上运行。

1. 在你的 `github-actions-demo` 的 GitHub 仓库页面上，点击绿色的 **Code** 按钮。
2. 确保选择了 **HTTPS** 标签页，并复制该 URL。它应该看起来像 `https://github.com/your-username/github-actions-demo.git`。
3. 在 LabEx 环境中打开终端。默认路径是 `~/project`。
4. 使用 `git clone` 命令下载仓库。将 `your-username` 替换为你实际的 GitHub 用户名。

```bash
cd ~/project
git clone https://github.com/your-username/github-actions-demo.git
```

5. 进入克隆下来的仓库：

```bash
cd ~/project/github-actions-demo
```

6. 在 `.github/workflows` 目录下创建一个名为 `simple-commands.yml` 的新工作流文件：

```bash
touch .github/workflows/simple-commands.yml
```

7. 在 WebIDE 中打开文件 `.github/workflows/simple-commands.yml`。你可以在左侧的文件浏览器中找到它。

8. 向文件中添加以下内容，以定义工作流名称、触发器、任务和运行器（runner）：

```yaml
name: Simple Commands
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
```

`runs-on` 关键字允许你指定运行该任务的机器类型。GitHub 为 Linux、Windows 和 macOS 提供了托管运行器（hosted runners）。`ubuntu-latest` 是一个常用的运行器，它提供了一个标准的 Ubuntu 环境。
