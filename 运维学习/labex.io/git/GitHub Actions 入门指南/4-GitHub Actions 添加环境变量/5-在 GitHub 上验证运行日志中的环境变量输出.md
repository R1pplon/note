# 在 GitHub 上验证运行日志中的环境变量输出

推送代码后，工作流将自动在 GitHub 上触发。你可以查看工作流运行日志，以验证环境变量是否被正确使用。

1. 在网页浏览器中访问你的 GitHub 仓库：`https://github.com/your-username/github-actions-demo`
2. 点击仓库页面顶部的 **Actions** 标签页。
3. 你应该会看到一个新的工作流运行被列出，名称可能为 "Update greeting variable"。

**注意：** 当你推送代码时，如果仓库中有多个工作流文件，可能会触发多个 Actions。请通过检查左侧边栏中的工作流名称或使用右侧的工作流运行列表来识别正确的工作流运行。

4. 点击运行标题，然后点击 **print-greeting** 任务（job）。
5. 展开 **Print Greeting** 步骤以查看输出。

你应该会看到命令执行结果：

```plaintext
Run echo "Hi, World!"
  echo "Hi, World!"
  shell: /usr/bin/bash -e {0}
Hi, World!
```

请注意，`${{ env.GREETING }}` 已被其新值 `Hi` 替换。

![GitHub Actions logs](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-adding-environment-variables/zh/../assets/20251127-15-12-38-9qqGaAXA.png)
