# 向 YAML 文件中添加 'name' 键，值为 'Hello World Workflow'

在这一步，你将开始编辑 YAML 文件来定义你的工作流（workflow）。我们通常添加的第一个元素是工作流的 `name`。这个名称将显示在 GitHub 仓库的 Actions 标签页中，便于识别。

使用 WebIDE 编辑器打开文件 `hello-world.yml`。你可以在左侧的文件浏览器中，于 `project/github-actions-demo/.github/workflows/` 目录下找到该文件。

向文件中添加以下内容：

```yaml
name: Hello World Workflow
```

这个键值对（key-value pair）为你的工作流分配了一个人类可读的名称。

添加内容后，保存文件（Ctrl+S 或 Cmd+S）。

你可以在终端中验证文件的内容：

```bash
cat .github/workflows/hello-world.yml
```

![Verify workflow name](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-first-workflow-creation/zh/../assets/20251127-14-15-34-t7dQEyjE.png)
