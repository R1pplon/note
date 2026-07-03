# 在 GitHub Actions 标签页查看工作流运行日志

推送代码后，工作流将自动在 GitHub 上触发。你可以查看工作流运行日志 (workflow run logs) 来了解发生了什么。

1. 在网页浏览器中访问你的仓库 (repository) 地址：`https://github.com/your-username/github-actions-demo`
2. 点击仓库页面顶部的 **Actions** 标签页。
3. 你应该会看到一个工作流运行列表，其名称可能为 "Simple Commands" 或 "Add simple commands workflow"。

**注意：** 当你推送代码时，如果仓库中有多个工作流文件，可能会触发多个 Actions。请通过检查左侧边栏中的工作流名称或使用右侧的工作流运行列表来识别正确的工作流运行。

4. 点击工作流运行的标题，然后点击 **build** 任务 (job)。
5. 展开 **Echo Hello**、**Show Date** 和 **List Files** 步骤，以查看它们的输出。

你会注意到 `List Files` 显示了一个空目录（除了隐藏文件如 `.` 和 `..`），这表明执行器 (runner) 是从一个干净的环境开始的。

![GitHub Actions logs](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-running-simple-commands/zh/../assets/20251127-14-59-20-IaJJ3nBC.png)

![GitHub Actions tab](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-running-simple-commands/zh/../assets/20251127-15-01-00-IdjqMnGg.png)
