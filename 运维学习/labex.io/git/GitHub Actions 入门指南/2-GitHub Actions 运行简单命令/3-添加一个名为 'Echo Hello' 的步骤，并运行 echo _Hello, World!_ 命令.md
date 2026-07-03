# 添加一个名为 'Echo Hello' 的步骤，并运行 echo "Hello, World!" 命令

在这一步中，你将向 `steps` 部分添加多个任务，以执行各种 shell 命令。这演示了你可以在 runner 上运行标准的 Linux 命令。

## 说明

1. 在 WebIDE 中打开 `.github/workflows/simple-commands.yml` 文件。
2. 在 `steps:` 键下添加以下步骤：

```yaml
steps:
  - name: Echo Hello
    run: echo "Hello, World!"

  - name: Show Date
    run: date

  - name: List Files
    run: ls -la
```

你的完整文件应如下所示：

```yaml
name: Simple Commands
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Echo Hello
        run: echo "Hello, World!"

      - name: Show Date
        run: date

      - name: List Files
        run: ls -la
```

## 解释

- `echo "Hello, World!"`: 将文本打印到控制台。
- `date`: 显示 runner 上的当前日期和时间。
- `ls -la`: 列出当前目录中的文件。你会注意到该目录几乎是空的，因为我们还没有检出（checkout）代码（我们将在下一个实验中进行此操作）。
