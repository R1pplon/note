# 管理 'at' 作业

在本步骤中，你将深入了解如何管理 `at` 作业，包括使用不同的队列调度作业以及验证其执行情况。了解 `at` 队列对于任务优先级排序或区分不同类型的一次性作业非常有用。

我们将继续在本地系统上进行操作，以探索更高级的 `at` 作业管理功能。

`at` 命令允许你使用 `-q` 选项指定一个队列。队列是 `a` 到 `z` 的单个字母。队列 `a` 是默认队列，队列 `a` 到 `z` 中的作业按优先级递减（niceness）执行。队列 `a` 具有最高优先级，队列 `z` 具有最低优先级。队列 `b` 保留给批处理作业。

让我们在队列 `g`（一个较低优先级的队列）中调度一个作业，使其在 2 分钟后运行。此作业将创建一个名为 `~/queue_g_job.txt` 的文件，其中包含一个时间戳：

```bash
at -q g now + 2 minutes << EOF
date > ~/queue_g_job.txt
EOF
```

你将看到类似 `job N at ...` 的输出。记下这个作业编号。

接下来，让我们调度另一个作业，这次是在队列 `b`（批处理队列）中，该队列通常用于可以在系统负载较低时运行的作业。此作业将向 `~/batch_job.txt` 追加 "Batch job executed!"。我们将其安排在 4 分钟后运行：

```bash
at -q b now + 4 minutes << EOF
echo "Batch job executed!" >> ~/batch_job.txt
EOF
```

同样，记下作业编号。

要查看所有待处理的作业，包括不同队列中的作业，请使用 `atq`。

```bash
atq
```

现在你应该能看到列出的两个作业，以及它们各自的队列字母（`g` 和 `b`）。

![atq 命令显示已调度作业的输出](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/rhel/rh134/lab-schedule-tasks-in-red-hat-enterprise-linux/zh/../assets/20250624-08-48-37-qMTvtvj0.png)

现在，等待你已调度的作业执行。至少等待 5 分钟，以便所有作业都能完成。你可以检查你的 `at` 作业创建的文件是否存在以及是否包含预期的内容。

检查 `~/queue_g_job.txt`：

```bash
cat ~/queue_g_job.txt
```

你应该能看到一个日期和时间字符串。

检查 `~/batch_job.txt`：

```bash
cat ~/batch_job.txt
```

你应该能看到 "Batch job executed!"。

如果文件不存在或为空，则可能意味着作业尚未执行，或者命令存在问题。你可以重新检查 `atq` 以查看它们是否仍在待处理状态。

这完成了高级 `at` 作业管理步骤。剩余的 `at` 作业将在容器销毁时自动清理。
