# 添加使用 run npm install 安装依赖的步骤

现在环境已经使用 Node.js 设置完毕，在持续集成（CI）流程中的下一步自然是安装项目的依赖项。这确保了在构建或测试之前，应用程序所需的所有外部库都可用。

再次打开 `.github/workflows/node-ci.yml` 文件，并添加安装依赖项的步骤。

将以下行添加到 `steps` 部分的末尾（请确保保持缩进）：

```yaml
- name: Install dependencies
  run: npm install
```

你完整的 `.github/workflows/node-ci.yml` 文件现在应该如下所示：

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
```

## 解释

- `run: npm install`: 这会在 runner 的 shell 中执行 `npm install` 命令。它会读取 `package.json` 文件（我们稍后会创建）并安装其中列出的依赖项。
