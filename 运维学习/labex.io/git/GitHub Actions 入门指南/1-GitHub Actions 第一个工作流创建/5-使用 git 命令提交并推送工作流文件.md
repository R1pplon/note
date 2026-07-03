# 使用 git 命令提交并推送工作流文件

在此步骤中，你将提交新的工作流文件并将其推送到 GitHub。这将触发你刚刚创建的工作流。

1. 确保你位于仓库目录中：

```bash
cd ~/project/github-actions-demo
```

2. 检查仓库状态，查看未跟踪的文件：

```bash
git status
```

3. 将新文件添加到暂存区：

```bash
git add .github/workflows/hello-world.yml
```

4. 使用描述性的消息提交更改：

```bash
git commit -m "Add hello world workflow"
```

**示例输出：**

```plaintext
[main ... ] Add hello world workflow
 1 file changed, X insertions(+), X deletions(-)
 create mode 100644 .github/workflows/hello-world.yml
```

5. 将更改推送到 GitHub 上的远程仓库：

```bash
git push
```

**关于认证的注意事项：**
当你运行 `git push` 时，WebIDE 将自动提示你进行身份验证。请遵循以下详细步骤：

1. 将出现一个弹出窗口，显示消息：**"The extension 'GitHub' wants to sign in using GitHub."** 点击 **Allow**。
2. 将出现一个新的通知。点击 **"Copy&Continue to GitHub"**，然后在下一个提示中点击 **"Open"**。
3. 在打开的浏览器窗口中登录你的 GitHub 账户，并输入已复制的授权码。确认授权后，页面将自动关闭。
4. 等待几秒钟，你将看到终端成功完成推送操作。

**隐私声明：** WebIDE 将请求完全访问你的 GitHub 账户以进行身份验证。你无需担心隐私问题——在完成当前实验（lab）后，LabEx VM 将立即销毁，你的凭证和授权信息将不会被保留。

此身份验证过程不需要手动配置用户名或 Personal Access Token。

遵循身份验证步骤后，你将看到类似以下内容的输出：

**示例输出：**

```plaintext
Enumerating objects: X, done.
Counting objects: 100% (X/X), done.
Delta compression using up to 4 threads
Compressing objects: 100% (X/X), done.
Writing objects: 100% (X/X), XXX bytes | XXX.00 KiB/s, done.
Total X (delta X), reused X (delta X), pack-reused 0
To https://github.com/your-username/github-actions-demo.git
   XXXXXXX..XXXXXXX  main -> main
```

7. 在 Web 浏览器中访问 GitHub 上的仓库，并导航到 **Actions** 标签页。你应该会看到 "Hello World Workflow" 正在运行或已完成。

![Verify workflow run](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-first-workflow-creation/zh/../assets/20251127-14-18-11-ZlVyKf37.png)

**注意：** 当你推送代码时，如果仓库中有多个工作流文件，可能会触发多个 Actions。请通过检查左侧边栏中的工作流名称或使用右侧的工作流运行列表来识别正确的工作流运行。

8. 点击工作流运行以查看详细的执行日志。你将看到：
    - **工作流名称**：顶部显示 "Hello World Workflow"
    - **Job 名称**：左侧边栏显示 "build"
    - **步骤执行**：点击 "build" job 展开它，然后点击 "Say Hello" 步骤
    - **输出**：你应该在日志中看到输出 `Hello, World!`

![Verify workflow run logs](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-first-workflow-creation/zh/../assets/20251127-14-23-34-bOaMLYJz.png)

这为你提供了工作流执行的完整视图，包括哪些步骤成功运行以及它们产生了什么输出。这对于调试和理解工作流的作用非常有用。
