# 添加 'jobs' 部分，其中包含一个名为 'build' 的 job

在这一步中，你将定义工作流 (workflow) 实际执行的工作内容。一次工作流运行 (workflow run) 由一个或多个 `jobs` 组成。默认情况下，实验 (jobs) 是并行运行的。

我们将添加一个名为 `build` 的 `jobs` 部分，其中包含一个实验。这个实验将在 Ubuntu 环境上运行，并执行一个简单的命令。

将以下内容追加到你的 `.github/workflows/hello-world.yml` 文件中：

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Say Hello
        run: echo "Hello, World!"
```

我们来分解一下这部分的作用：

- `jobs:`: 开始实验部分。
- `build:`: 实验的 ID。
- `runs-on: ubuntu-latest`: 指定在何种类型的机器（runner）上运行该实验。
- `steps:`: 实验中要运行的任务列表。
- `run: echo "Hello, World!"`: 要执行的 shell 命令。

现在你的完整文件应该如下所示：

```yaml
name: Hello World Workflow
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Say Hello
        run: echo "Hello, World!"
```

保存文件。
