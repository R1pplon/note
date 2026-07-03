# 添加 'on' 键并配置 'push' 事件以在推送时触发

在这一步，你将定义工作流 *何时* 运行。这是使用 `on` 关键字完成的。我们希望每当代码被推送到仓库时，此工作流都能运行。

再次打开 `.github/workflows/hello-world.yml` 文件，并在 `name` 键的下方追加以下行：

```yaml
on: [push]
```

你的文件现在应该看起来像这样：

```yaml
name: Hello World Workflow
on: [push]
```

`on: [push]` 配置告诉 GitHub Actions 在每次对仓库中任何分支执行 `git push` 时触发此工作流。

**注意：** GitHub Actions 支持许多其他的触发事件。例如：

- `pull_request`: 在拉取请求（pull request）被打开、同步或关闭时触发
- `workflow_dispatch`: 允许从 GitHub Actions 标签页手动触发
- `schedule`: 使用 cron 语法按计划运行
- `release`: 在发布（release）被发布时触发
- 以及更多事件，如 `issues`、`pull_request_review` 等。

在这个 Lab 中，我们使用 `on: [push]`，因为它最简单且最常见的触发器。在你深入学习 GitHub Actions 的过程中，可以探索其他触发器类型。

修改后保存文件。
