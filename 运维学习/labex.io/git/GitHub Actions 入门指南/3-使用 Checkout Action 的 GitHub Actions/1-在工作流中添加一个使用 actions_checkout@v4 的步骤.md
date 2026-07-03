# 在工作流中添加一个使用 actions/checkout@v4 的步骤

在这一步，你将克隆（clone）仓库并创建一个使用 `actions/checkout` 操作的工作流（workflow）。此操作会将你的仓库检出（checks out）到 `$GITHUB_WORKSPACE` 目录下，以便你的工作流可以访问它。

1. 在你的 `github-actions-demo` 的 GitHub 仓库页面上，点击绿色的 **Code** 按钮。
2. 确保选择了 **HTTPS** 标签页，并复制该 URL。它应该看起来像 `https://github.com/your-username/github-actions-demo.git`。
3. 在 LabEx 环境中打开终端。默认路径是 `~/project`。
4. 使用 `git clone` 命令下载仓库。将 `your-username` 替换为你真实的 GitHub 用户名。

```bash
cd ~/project
git clone https://github.com/your-username/github-actions-demo.git
```

5. 进入克隆的仓库目录：

```bash
cd ~/project/github-actions-demo
```

6. 在 `.github/workflows` 目录下创建一个名为 `ci.yml` 的新工作流文件：

```bash
touch .github/workflows/ci.yml
```

7. 在 WebIDE 编辑器中打开 `.github/workflows/ci.yml` 并添加以下内容。此配置定义了一个名为 "CI" 的工作流，它在每次 `push` 事件时运行。它包含一个名为 `build` 的单一任务（job），在 `ubuntu-latest` 上运行，其中包含一个检出代码的步骤（step）。

```yaml
name: CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4
```

`uses: actions/checkout@v4` 这行代码告诉 GitHub Actions 下载并运行官方 checkout 操作的 `v4` 版本。
