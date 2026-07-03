# 添加一个使用 ls -la 命令来列出文件的步骤

在这一步中，你将向工作流中添加第二个步骤，以验证 checkout action 是否成功检索了你的文件。你将使用一个标准的 shell 命令来列出当前目录中的文件。

打开 `.github/workflows/ci.yml` 文件，并将以下步骤追加到 `steps` 列表中。确保缩进与上一个步骤正确对齐。

```yaml
- name: List files
  run: ls -la
```

你完整的 `.github/workflows/ci.yml` 文件现在应该如下所示：

```yaml
name: CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: List files
        run: ls -la
```

`run` 关键字允许你执行命令行程序。在这里，`ls -la` 将列出仓库根目录下所有文件，包括隐藏文件。
