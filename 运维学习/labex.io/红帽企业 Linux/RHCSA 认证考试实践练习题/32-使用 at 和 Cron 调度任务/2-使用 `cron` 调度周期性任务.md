# 使用 `cron` 调度周期性任务

`cron` 守护进程用于按定期的时间间隔执行预定命令。你可以使用 `crontab` 命令管理你的调度任务。

## 任务

- 创建一个每分钟运行一次命令的 cron 作业。
- 验证 cron 作业是否创建并更新了日志文件。

## 要求

- 使用 `crontab -e` 命令编辑当前用户的 crontab。
- 添加一个 cron 作业，执行命令 `echo "This is a recurring task" >> /home/labex/project/recurring_task.log`。
- 该作业必须配置为每天、每小时的每一分钟运行。
- 文件 `/home/labex/project/recurring_task.log` 应该被创建，并且每分钟都会追加新的文本。

## 示例

在 cron 作业运行几分钟后，`/home/labex/project/recurring_task.log` 的内容应类似于：

```plaintext
This is a recurring task
This is a recurring task
This is a recurring task
```

## 提示

- `crontab -e` 命令会在文本编辑器（通常是 `vi`）中打开你的 crontab 文件。
- 每分钟运行一次的 cron 作业格式为 `* * * * * command_to_run`。
- 如果编辑器是 `vi`，按 `i` 进入插入模式，输入你的内容，按 `Esc` 退出插入模式，然后输入 `:wq` 并按回车键保存并退出。
