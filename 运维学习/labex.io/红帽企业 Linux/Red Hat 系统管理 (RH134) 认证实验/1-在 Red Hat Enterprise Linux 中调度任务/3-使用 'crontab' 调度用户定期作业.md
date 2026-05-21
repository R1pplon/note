# 使用 'crontab' 调度用户定期作业

在本步骤中，你将学习如何为特定用户调度周期性任务，使用 `crontab`。与一次性运行的 `at` 任务不同，`cron` 任务会在指定的时间间隔内重复运行。这非常适合例行维护、数据备份或生成报告。

我们将继续在本地系统上学习用户 crontab 管理。

`crontab` 命令允许用户创建、编辑和查看他们自己的 `cron` 任务。每个用户都有自己的 `crontab` 文件。

要编辑你的 `crontab` 文件，请使用 `crontab -e` 命令。这将在默认文本编辑器（通常是 `vim`）中打开你的 `crontab` 文件。

```bash
crontab -e
```

**Vim 编辑器说明：**

- 按 `i` 进入插入模式（你会在底部看到 `-- INSERT --`）
- 使用箭头键导航
- 保存并退出：按 `Esc` 退出插入模式，然后输入 `:wq` 并按 `Enter`
- 不保存退出：按 `Esc`，然后输入 `:q!` 并按 `Enter`

在编辑器中，你将添加一个新行来定义你的 `cron` 任务。一个 `cron` 条目包含五个时间日期字段，后面是要执行的命令。这些字段是：

- **分钟 (0-59)**
- **小时 (0-23)**
- **日期 (1-31)**
- **月份 (1-12)**
- **星期几 (0-7，其中 0 或 7 代表星期日)**

你可以使用 `*` 作为通配符，表示某个字段的“每”，或者使用 `/` 来指定步长值（例如，`*/5` 表示每 5 分钟）。

让我们来调度一个任务，该任务每分钟将当前日期和时间追加到名为 `~/my_cron_log.txt` 的文件中。这将使我们能够快速观察到 `cron` 任务的运行情况。

在 vim 中按照以下步骤操作：

1. 按 `i` 进入插入模式
2. 向 `crontab` 文件添加以下行：

```
* * * * * /usr/bin/date >> ~/my_cron_log.txt
```

3. 按 `Esc` 退出插入模式
4. 输入 `:wq` 并按 `Enter` 保存并退出

你应该会看到一条消息，表明已安装新的 crontab：

```plaintext
crontab: installing new crontab
```

要验证你的 `cron` 任务是否已成功添加，你可以使用 `crontab -l` 命令列出你的 crontab 条目：

```bash
crontab -l
```

你应该会看到你刚刚添加的行：

```plaintext
* * * * * /usr/bin/date >> ~/my_cron_log.txt
```

现在，等待一两分钟，让 `cron` 任务至少运行一次。你可以查看当前时间，了解下一个分钟标记何时到来：

```bash
date
```

等待至少两分钟，让 cron 任务运行几次后，检查 `~/my_cron_log.txt` 文件的内容。

```bash
cat ~/my_cron_log.txt
```

你应该会看到一行或多行，每行包含一个日期和时间，表明你的 `cron` 任务已执行。

```plaintext
Mon Apr 8 10:30:01 AM EDT 2024
Mon Apr 8 10:31:01 AM EDT 2024
```

![Cron job output in log file](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/rhel/rh134/lab-schedule-tasks-in-red-hat-enterprise-linux/zh/../assets/20250624-08-52-40-IpAHQkr2.png)

这完成了用户 crontab 管理步骤。该 cron 任务将继续运行，直到容器被销毁。
