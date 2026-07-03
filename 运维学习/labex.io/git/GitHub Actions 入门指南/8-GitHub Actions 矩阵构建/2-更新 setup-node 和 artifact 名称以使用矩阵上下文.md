# 更新 setup-node 和 artifact 名称以使用矩阵上下文

定义矩阵只是成功的一半；我们还需要告诉步骤如何使用矩阵中的当前值。我们通过 `${{ matrix.node-version }}` 上下文来实现这一点。

我们还需要确保每个并行实验（job）上传的工件（artifact）具有唯一的名称，否则它们会相互覆盖。

1. 在 `.github/workflows/matrix-build.yml` 文件中，验证 `Use Node.js` 步骤使用了矩阵变量：

```yaml
- name: Use Node.js
  uses: actions/setup-node@v4
  with:
    node-version: ${{ matrix.node-version }}
```

2. 验证 `Upload build artifact` 步骤在工件名称中包含了矩阵版本：

```yaml
- name: Upload build artifact
  uses: actions/upload-artifact@v4
  with:
    name: build-assets-${{ matrix.node-version }}
    path: dist
```

3. 你的完整工作流（workflow）文件应如下所示：

```yaml
name: Matrix Build

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18, 20, 22]

    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - name: Install dependencies
        run: npm install
      - name: Build project
        run: |
          mkdir dist
          echo "This is the build artifact" > dist/build.txt
      - name: Run tests
        run: npm test
      - name: Upload build artifact
        uses: actions/upload-artifact@v4
        with:
          name: build-assets-${{ matrix.node-version }}
          path: dist
```

保存文件（Ctrl+S 或 Cmd+S）。
