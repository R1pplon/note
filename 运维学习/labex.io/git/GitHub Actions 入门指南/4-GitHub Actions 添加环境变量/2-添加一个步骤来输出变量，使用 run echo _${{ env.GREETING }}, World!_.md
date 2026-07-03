# 添加一个步骤来输出变量，使用 run echo "${{ env.GREETING }}, World!"

在此步骤中，你将定义一个 job 和一个 step 来使用你创建的环境变量。你在工作流文件（workflow file）中使用 `${{ env.VARIABLE_NAME }}` 语法来访问环境变量。

在 WebIDE 中打开 `.github/workflows/env-demo.yml` 文件，并将以下内容追加到文件末尾：

```yaml
jobs:
  print-greeting:
    runs-on: ubuntu-latest
    steps:
      - name: Print Greeting
        run: echo "${{ env.GREETING }}, World!"
```

你的完整文件应如下所示：

```yaml
name: Environment Variable Demo

on: [push]

env:
  GREETING: "Hello"

jobs:
  print-greeting:
    runs-on: ubuntu-latest
    steps:
      - name: Print Greeting
        run: echo "${{ env.GREETING }}, World!"
```

- `jobs`: 定义工作流运行的 job。
- `print-greeting`: job 的 ID。
- `runs-on`: 配置运行该 job 的机器类型（runner）。
- `steps`: 将在 job 中运行的所有步骤组合在一起。
- `run`: 在 runner 的 shell 中执行一个命令。我们使用 `echo` 来打印 `GREETING` 的值，后跟 `, World!`。
