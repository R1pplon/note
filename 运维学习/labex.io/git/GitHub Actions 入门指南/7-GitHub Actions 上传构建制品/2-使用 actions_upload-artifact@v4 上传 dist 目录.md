# 使用 actions/upload-artifact@v4 上传 dist 目录

既然我们已经生成了一些文件，现在需要将它们上传。我们将使用官方的 `actions/upload-artifact` Action。

1. 在 `.github/workflows/upload-artifacts.yml` 文件中，在 Job 的末尾添加一个新的步骤 (step)：

```yaml
- name: Upload build artifact
  uses: actions/upload-artifact@v4
  with:
    name: build-assets
    path: dist
```

2. 你的完整文件看起来应该与此类似：

```yaml
name: Upload Artifacts

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
      - name: Build project
        run: |
          mkdir dist
          echo "This is the build artifact" > dist/build.txt
      - name: Run tests
        run: npm test
      - name: Upload build artifact
        uses: actions/upload-artifact@v4
        with:
          name: build-assets
          path: dist
```

## 解释 (Explanation)

- `uses: actions/upload-artifact@v4`: 调用 Action 来处理上传。
- `name: build-assets`: 构建产物在 GitHub UI 中显示的名称。
- `path: dist`: 要上传的目录（或文件）。

保存文件 (Ctrl+S 或 Cmd+S)。
