# 使用 `at` 调度一次性任务

`at` 命令用于调度在未来某个特定时间执行一次的命令。

## 任务

- 使用 `at` 命令调度一个在一分钟后运行的任务。
- 验证该任务是否创建了包含指定内容的文件。

## 要求

- 调度一个从现在起一分钟后运行的任务。
- 该任务必须执行命令 `echo "This is a scheduled task" > /home/labex/project/scheduled_task.txt`。
- 任务执行后，文件 `/home/labex/project/scheduled_task.txt` 必须存在，并且包含文本 `This is a scheduled task`。

## 示例

任务执行后，`/home/labex/project/scheduled_task.txt` 的内容应为：

```plaintext
This is a scheduled task
```

## 提示

- 使用时间规格 `now + 1 minute` 来调度任务。
- 输入 `at` 命令后，你将进入一个新的提示符界面。输入你的命令并按回车键。
- 要保存作业并退出 `at` 提示符，请按 `Ctrl+D`。
