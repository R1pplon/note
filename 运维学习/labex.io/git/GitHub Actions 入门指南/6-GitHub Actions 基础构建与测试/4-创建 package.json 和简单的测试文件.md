# 创建 package.json 和简单的测试文件

为了我们刚刚创建的工作流能够真正运行，我们需要一个 Node.js 项目结构。具体来说，`npm install` 需要一个 `package.json` 文件，而 `npm test` 需要定义一个测试脚本。

1. 确保你位于仓库目录中：

```bash
cd ~/project/github-actions-demo
```

2. 在仓库根目录创建一个 `package.json` 文件：

```bash
touch package.json
```

3. 打开 `package.json` 并添加以下内容。这定义了一个基础项目和一个总是通过（以代码 0 退出）的简单测试脚本。

```json
{
  "name": "github-actions-demo",
  "version": "1.0.0",
  "description": "Demo project",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Running tests...\" && exit 0"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

4. 接下来，我们更新 `index.js` 以代表我们的应用代码。

```bash
echo "console.log('Hello, GitHub Actions!');" > index.js
```

现在你拥有了一个满足我们定义的**工作流**要求的最小 Node.js 项目：

1. `npm install` 将会运行（即使没有依赖项，它也会检查 `package.json`）。
2. `npm test` 将会运行命令 `echo "Running tests..." && exit 0`。
