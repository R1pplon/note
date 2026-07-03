# 在仓库中创建一个名为 index.js 的示例文件

在这一步，你将在你的仓库中创建一个示例文件。当我们在工作流日志中看到它被列出时，这个文件将作为 `actions/checkout` 动作正常工作的证明。

1. 确保你位于仓库目录下：

```bash
cd ~/project/github-actions-demo
```

2. 在你的仓库根目录下创建一个名为 `index.js` 的简单 JavaScript 文件：

```bash
echo "console.log('Hello, GitHub Actions!');" > index.js
```

3. 你可以通过运行以下命令来验证文件是否创建成功：

```bash
ls -l index.js
```

这个文件代表了你的应用程序的源代码，通常 CI 流水线会对它进行构建或测试。
