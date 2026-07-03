# 定义依赖于构建任务的部署任务 (deploy job)

现在我们将添加第二个名为 `deploy` 的任务。此任务仅应在 `build` 任务成功时运行。我们通过使用 `needs: build` 来实现这一点。

1. 将以下 `deploy` 任务追加到你的 `.github/workflows/job-dependencies.yml` 文件中。确保它的缩进级别与 `build` 任务相同。

2. 首先，添加部署任务的定义，包括其运行环境（runner）和依赖项：

```yaml
deploy:
  runs-on: ubuntu-latest
  needs: build
```

`needs: build` 这一行至关重要——它告诉 GitHub Actions 此任务依赖于 `build` 任务的成功完成。

3. 添加步骤（steps）部分。首先，添加下载工件（artifact）的步骤：

```yaml
steps:
  - name: Download artifact
    uses: actions/download-artifact@v4
    with:
      name: dist-files
      path: dist
```

这会下载在 `build` 任务中上传的工件。`name` 必须与上传步骤中使用的名称相匹配。

4. 添加部署步骤：

```yaml
- name: Deploy project
  run: |
    echo "Deploying project..."
    ls -R dist
    echo "Deployment successful!"
```

此步骤通过列出已下载的文件来模拟部署过程。

2. 你的完整文件结构应如下所示：

```yaml
name: Job Dependencies

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
      - name: Build project
        run: |
          mkdir dist
          echo "Build artifact created at $(date)" > dist/build.txt
      - name: Upload build artifact
        uses: actions/upload-artifact@v4
        with:
          name: dist-files
          path: dist

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v4
        with:
          name: dist-files
          path: dist
      - name: Deploy project
        run: |
          echo "Deploying project..."
          ls -R dist
          echo "Deployment successful!"
```

## 解释

- `needs: build`: 这是关键的一行。它告诉 GitHub Actions 此任务依赖于 `build` 任务的成功完成。
- `uses: actions/download-artifact@v4`: 由于任务在不同的虚拟机上运行，它们不共享文件系统。为了获取在 `build` 任务中创建的 `dist` 文件夹，我们必须下载之前上传的工件。
- `name: dist-files`: 必须与上传步骤中使用的名称相匹配。

保存文件 (Ctrl+S 或 Cmd+S)。
