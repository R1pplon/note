# 添加使用 run npm test 运行测试的步骤

安装依赖项后，持续集成（Continuous Integration）中最关键的部分是运行自动化实验。这确保了新的更改不会破坏现有功能。

打开 `.github/workflows/node-ci.yml` 文件，并在其中追加运行实验的步骤。

将以下行添加到 `steps` 部分的末尾：

```yaml
- name: Run tests
  run: npm test
```

你最终的 `.github/workflows/node-ci.yml` 文件应如下所示：

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
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
```

## 解释

- `run: npm test`: 这会执行在你的 `package.json` 中定义的实验脚本。如果实验失败（以非零代码退出），GitHub Action 工作流将被标记为失败。
