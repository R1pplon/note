# 添加 env 部分，并将变量 GREETING 设置为 'Hello'

在这一步中，你将克隆仓库并创建一个带有全局环境变量的新 GitHub Actions 工作流文件。

1. 在你的 `github-actions-demo` 的 GitHub 仓库页面上，点击绿色的 **Code** 按钮。
2. 确保选择了 **HTTPS** 标签页，并复制该 URL。它应该看起来像 `https://github.com/your-username/github-actions-demo.git`。
3. 在 LabEx 环境中打开终端。默认路径是 `~/project`。
4. 使用 `git clone` 命令下载仓库。将 `your-username` 替换为你真实的 GitHub 用户名。

```bash
cd ~/project
git clone https://github.com/your-username/github-actions-demo.git
```

5. 进入克隆的仓库：

```bash
cd ~/project/github-actions-demo
```

6. 在 `.github/workflows` 目录下创建一个名为 `env-demo.yml` 的新文件：

```bash
touch .github/workflows/env-demo.yml
```

7. 在 WebIDE 中打开 `.github/workflows/env-demo.yml` 并添加以下内容。这定义了工作流的名称、触发事件（push）以及带有名为 `GREETING` 的变量的 `env` 部分。

```yaml
name: Environment Variable Demo

on: [push]

env:
  GREETING: "Hello"
```

- `name`: 你的工作流的名称。
- `on`: 指定当发生 `push` 事件时运行此工作流。
- `env`: 定义一个环境变量映射，这些变量可用于工作流中的所有 job 和 step。这里，我们将 `GREETING` 设置为字符串 `'Hello'`。
