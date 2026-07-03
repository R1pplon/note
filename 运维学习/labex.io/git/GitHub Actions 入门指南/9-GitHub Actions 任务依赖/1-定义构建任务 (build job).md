# 定义构建任务 (build job)

首先，我们将清理现有的工作流（workflow），以专注于一个 `build` 实验。为了清晰起见，我们将简化前一个 Lab 中的矩阵策略（matrix strategy），恢复使用单一版本，以便将重点放在实验依赖关系上。

1. 在你的 GitHub 仓库页面 `github-actions-demo` 上，点击绿色的 **Code** 按钮。
2. 确保选择了 **HTTPS** 标签页，并复制 URL。它应该看起来像 `https://github.com/your-username/github-actions-demo.git`。
3. 在 LabEx 环境中打开终端。默认路径是 `~/project`。
4. 使用 `git clone` 命令下载仓库。将 `your-username` 替换为你的实际 GitHub 用户名。

```bash
cd ~/project
git clone https://github.com/your-username/github-actions-demo.git
```

**示例输出：**

```plaintext
Cloning into 'github-actions-demo'...
remote: Enumerating objects: X, done.
remote: Counting objects: 100% (X/X), done.
remote: Total X (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (X/X), done.
```

5. 进入克隆下来的仓库：

```bash
cd ~/project/github-actions-demo
```

6. 使用 WebIDE 编辑器创建一个新的工作流文件 `.github/workflows/job-dependencies.yml`。你可以在左侧文件浏览器中 `project/github-actions-demo/.github/workflows/` 下找到该文件。

7. 首先创建基本的工作流结构。添加工作流名称和触发器：

```yaml
name: Job Dependencies

on: [push]
```

8. 添加 `jobs` 部分并定义 `build` 实验及其运行器（runner）：

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

9. 添加 `steps` 部分。首先，添加 `checkout` 步骤以下载仓库代码：

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

11. 添加安装依赖项的步骤：

```yaml
- name: Install dependencies
  run: npm install
```

12. 添加运行测试的步骤：

```yaml
- name: Run tests
  run: npm test
```

13. 添加构建步骤，该步骤创建工件（artifact）目录和文件：

```yaml
- name: Build project
  run: |
    mkdir dist
    echo "Build artifact created at $(date)" > dist/build.txt
```

14. 最后，添加上传工件的步骤。此步骤至关重要，因为它会保存构建输出，以便下一个实验可以使用它：

```yaml
- name: Upload build artifact
  uses: actions/upload-artifact@v4
  with:
    name: dist-files
    path: dist
```

你的完整文件现在应该如下所示：

```yaml
name: Job Dependencies

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
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
      - name: Build project
        run: |
          mkdir dist
          echo "Build artifact created at $(date)" > dist/build.txt
      - name: Upload build artifact
        uses: actions/upload-artifact@v4
        with:
          name: dist-files
          path: dist
```

## 解释

- 我们为了简化而移除了 `matrix` 策略。
- 我们保留了 `Upload build artifact` 步骤。这至关重要，因为下一个实验将需要这些文件！

完成更改后，保存文件（Ctrl+S 或 Cmd+S）。
