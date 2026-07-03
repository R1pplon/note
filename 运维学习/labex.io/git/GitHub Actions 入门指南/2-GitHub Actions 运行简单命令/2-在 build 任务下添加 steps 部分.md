# 在 build 任务下添加 steps 部分

现在你已经定义了工作环境（job environment），你需要定义该工作实际执行的操作。这是使用 `steps` 关键字来完成的。

一个工作（job）包含一系列称为 `steps` 的任务。步骤（steps）可以运行命令（commands）、运行设置任务（setup tasks）或运行你仓库中的一个动作（action）。

## 说明

1. 在 WebIDE 中打开 `.github/workflows/simple-commands.yml` 文件。
2. 在 `build` 工作（job）下添加 `steps` 关键字。

将你的文件更新为如下所示：

```yaml
name: Simple Commands
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
```

确保 `steps:` 与 `runs-on:` 处于相同的缩进级别。
