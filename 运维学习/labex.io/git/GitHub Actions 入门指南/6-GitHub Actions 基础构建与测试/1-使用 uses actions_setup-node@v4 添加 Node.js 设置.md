# 使用 uses actions/setup-node@v4 添加 Node.js 设置

在这一步，你将克隆（clone）仓库并创建一个带有 Node.js 环境设置的工作流（workflow）文件。GitHub Actions 工作流定义在仓库的 `.github/workflows` 目录下的 YAML 文件中。

1. 在你的 `github-actions-demo` 的 GitHub 仓库页面上，点击绿色的 **Code** 按钮。
2. 确保选择了 **HTTPS** 标签页，并复制该 URL。它看起来应该像 `https://github.com/your-username/github-actions-demo.git`。
3. 在 LabEx 环境中打开终端。默认路径是 `~/project`。
4. 使用 `git clone` 命令下载仓库。将 `your-username` 替换为你实际的 GitHub 用户名。

```bash
cd ~/project
git clone https://github.com/your-username/github-actions-demo.git
```

5. 进入克隆下来的仓库目录：

```bash
cd ~/project/github-actions-demo
```

6. 在 `.github/workflows` 目录下创建一个名为 `node-ci.yml` 的新文件：

```bash
touch .github/workflows/node-ci.yml
```

7. 在 WebIDE 中打开 `.github/workflows/node-ci.yml`，首先添加工作流名称和触发器：

```yaml
name: Node.js CI

on: [push]
```

8. 添加 `jobs`（任务）部分，并定义 `build` 任务及其运行器（runner）：

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

9. 添加 `steps`（步骤）部分。首先，添加 `checkout` 步骤以下载仓库代码：

```yaml
steps:
  - uses: actions/checkout@v4
```

10. 添加 Node.js 设置步骤：

```yaml
- name: Use Node.js
  uses: actions/setup-node@v4
  with:
    node-version: "20"
```

现在你的完整文件应该如下所示：

```yaml
name: Node.js CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"
```

## 解释

- `name`: 工作流的名称。
- `on: [push]`: 每当有代码被推送到仓库时触发工作流。
- `jobs`: 将在工作流中运行的所有任务组合在一起。
- `runs-on: ubuntu-latest`: 配置任务在 GitHub 托管的、运行最新 Ubuntu 版本的虚拟机上执行。
- `steps`: 一系列任务。
- `uses: actions/checkout@v4`: 检出（checks out）你的仓库，以便工作流可以访问它。
- `uses: actions/setup-node@v4`: 这是在运行器（runner）上安装 Node.js 的官方 Action。我们指定版本 '20'。
