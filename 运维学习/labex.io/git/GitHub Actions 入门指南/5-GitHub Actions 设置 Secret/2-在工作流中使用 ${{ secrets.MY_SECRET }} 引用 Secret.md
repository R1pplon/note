# 在工作流中使用 ${{ secrets.MY_SECRET }} 引用 Secret

在这一步，你将创建一个引用了你刚刚创建的 Secret 的工作流文件。

1. 确保你位于仓库目录中：

```bash
cd ~/project/github-actions-demo
```

2. 在 `.github/workflows` 目录下创建一个名为 `secrets-demo.yml` 的新文件：

```bash
touch .github/workflows/secrets-demo.yml
```

3. 在 WebIDE 中打开 `.github/workflows/secrets-demo.yml` 并添加以下内容。这定义了一个在 push 事件触发时的基础工作流。

```yaml
name: Secrets Demo
on: [push]

jobs:
  use-secret:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
```

这设置好了 Job。在下一步中，我们将添加使用 Secret 的逻辑。
