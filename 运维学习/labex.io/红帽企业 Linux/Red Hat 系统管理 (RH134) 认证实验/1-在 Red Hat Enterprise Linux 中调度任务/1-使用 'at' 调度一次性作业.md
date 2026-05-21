# 使用 'at' 调度一次性作业

在本步骤中，你将学习如何使用 `at` 命令调度一个任务在未来的某个时间点仅运行一次。`at` 命令适用于执行不需要重复运行的命令。我们将调度一个简单的任务，检查其详细信息，然后将其移除。

在本实验 (lab) 中，我们将直接在本地系统上进行操作，以学习任务调度。所有命令都将在你当前的终端环境中执行。

让我们调度一个任务，将当前日期和时间打印到你主目录下的 `~/myjob.txt` 文件中。我们将安排它在 3 分钟后运行：

```bash
at now + 3 minutes << EOF
date > ~/myjob.txt
EOF
```

`warning: commands will be executed using /bin/sh` 消息是正常的。`job N at ...` 输出表明了任务编号和计划的执行时间。请记下任务编号，因为稍后你会用到它。

接下来，让我们以交互方式调度另一个任务。这种方法适用于输入多个命令或更复杂的脚本。我们将调度一个任务，在 5 分钟后将 "Hello from at job!" 追加到 `~/at_output.txt` 中：

```bash
at now + 5 minutes
```

输入命令并按 Enter 后，你将看到一个 `at>` 提示符。输入你的命令，然后按 `Ctrl+d` 完成：

```bash
at > echo "Hello from at job!" >> ~/at_output.txt
at > Ctrl+d
```

要查看当前 `at` 队列中的任务，请使用 `atq` 命令。此命令会列出当前用户所有待处理的 `at` 任务。

```bash
atq
```

输出将显示任务编号、计划时间、队列以及调度该任务的用户。

![atq 命令输出显示已计划的任务](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/rhel/rh134/lab-schedule-tasks-in-red-hat-enterprise-linux/zh/../assets/20250624-08-46-57-pDV2xwnh.png)

你可以使用 `at -c` 命令加上任务编号来检查特定 `at` 任务将要运行的命令。将 `N` 替换为你之前记下的任务编号之一。

```bash
at -c N
```

此命令将显示 `at` 将为该任务执行的 shell 脚本。你应该在输出中看到 `date > ~/myjob.txt` 或 `echo "Hello from at job!" >> ~/at_output.txt` 命令。

最后，要移除已计划的 `at` 任务，请使用 `atrm` 命令加上任务编号。让我们移除我们第一个调度的任务。将 `N` 替换为你第一个任务的任务编号。

```bash
atrm N
```

移除任务后，你可以再次使用 `atq` 来验证它是否已不再队列中。

```bash
atq
```

你应该只能看到第二个任务（如果它尚未执行）或者一个空队列（如果两个任务都已被移除或执行）。

至此，使用 `at` 命令调度一次性任务的第一步已完成。
