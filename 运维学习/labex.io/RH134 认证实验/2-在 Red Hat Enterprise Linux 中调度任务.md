## 介绍

在这个实验中，你将通过使用各种工具，获得在 RHEL 系统上调度任务的实践经验。你将学习使用 `at` 命令调度一次性作业，使用 `crontab` 管理用户特定的重复任务，以及使用 cron 目录配置系统范围内的重复作业。

此外，本实验还将涵盖使用 `systemd` 定时器进行更强大、更灵活的任务自动化的高级调度技术，并演示如何使用 `systemd-tmpfiles` 有效地管理临时文件。通过本实验，你将熟练掌握为不同场景选择合适的调度方法，并在 RHEL 环境中有效地管理自动化任务。

## 使用 'at' 调度一次性作业

在本步骤中，你将学习如何使用 `at` 命令调度一个任务在未来的某个时间点仅运行一次。`at` 命令适用于执行不需要重复运行的命令。我们将调度一个简单的任务，检查其详细信息，然后将其移除。

在本实验 (lab) 中，我们将直接在本地系统上进行操作，以学习任务调度。所有命令都将在你当前的终端环境中执行。

让我们调度一个任务，将当前日期和时间打印到你主目录下的 `~/myjob.txt` 文件中。我们将安排它在 3 分钟后运行：

```bash
at now + 3 minutes << EOF
date > ~/myjob.txt
EOF
```

`warning: commands will be executed using /bin/sh` 消息是正常的。`job N at ...` 输出表明了任务编号和计划的执行时间。请记下任务编号，因为稍后你会用到它。

接下来，让我们以交互方式调度另一个任务。这种方法适用于输入多个命令或更复杂的脚本。我们将调度一个任务，在 5 分钟后将 "Hello from at job!" 追加到 `~/at_output.txt` 中：

```bash
at now + 5 minutes
```

输入命令并按 Enter 后，你将看到一个 `at>` 提示符。输入你的命令，然后按 `Ctrl+d` 完成：

```bash
at > echo "Hello from at job!" >> ~/at_output.txt
at > Ctrl+d
```

要查看当前 `at` 队列中的任务，请使用 `atq` 命令。此命令会列出当前用户所有待处理的 `at` 任务。

```bash
atq
```

输出将显示任务编号、计划时间、队列以及调度该任务的用户。

![atq 命令输出显示已计划的任务](https://labex.io/cdn-cgi/image/format=auto,quality=60,onerror=redirect/https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/rhel/rh134/lab-schedule-tasks-in-red-hat-enterprise-linux/assets/20250624-08-46-57-pDV2xwnh.png)

你可以使用 `at -c` 命令加上任务编号来检查特定 `at` 任务将要运行的命令。将 `N` 替换为你之前记下的任务编号之一。

```bash
at -c N
```

此命令将显示 `at` 将为该任务执行的 shell 脚本。你应该在输出中看到 `date > ~/myjob.txt` 或 `echo "Hello from at job!" >> ~/at_output.txt` 命令。

最后，要移除已计划的 `at` 任务，请使用 `atrm` 命令加上任务编号。让我们移除我们第一个调度的任务。将 `N` 替换为你第一个任务的任务编号。

```bash
atrm N
```

移除任务后，你可以再次使用 `atq` 来验证它是否已不再队列中。

```bash
atq
```

你应该只能看到第二个任务（如果它尚未执行）或者一个空队列（如果两个任务都已被移除或执行）。

至此，使用 `at` 命令调度一次性任务的第一步已完成。

## 管理 'at' 作业

在本步骤中，你将深入了解如何管理 `at` 作业，包括使用不同的队列调度作业以及验证其执行情况。了解 `at` 队列对于任务优先级排序或区分不同类型的一次性作业非常有用。

我们将继续在本地系统上进行操作，以探索更高级的 `at` 作业管理功能。

`at` 命令允许你使用 `-q` 选项指定一个队列。队列是 `a` 到 `z` 的单个字母。队列 `a` 是默认队列，队列 `a` 到 `z` 中的作业按优先级递减（niceness）执行。队列 `a` 具有最高优先级，队列 `z` 具有最低优先级。队列 `b` 保留给批处理作业。

让我们在队列 `g`（一个较低优先级的队列）中调度一个作业，使其在 2 分钟后运行。此作业将创建一个名为 `~/queue_g_job.txt` 的文件，其中包含一个时间戳：

```bash
at -q g now + 2 minutes << EOF
date > ~/queue_g_job.txt
EOF
```

你将看到类似 `job N at ...` 的输出。记下这个作业编号。

接下来，让我们调度另一个作业，这次是在队列 `b`（批处理队列）中，该队列通常用于可以在系统负载较低时运行的作业。此作业将向 `~/batch_job.txt` 追加 "Batch job executed!"。我们将其安排在 4 分钟后运行：

```bash
at -q b now + 4 minutes << EOF
echo "Batch job executed!" >> ~/batch_job.txt
EOF
```

同样，记下作业编号。

要查看所有待处理的作业，包括不同队列中的作业，请使用 `atq`。

```bash
atq
```

现在你应该能看到列出的两个作业，以及它们各自的队列字母（`g` 和 `b`）。

![atq 命令显示已调度作业的输出](https://labex.io/cdn-cgi/image/format=auto,quality=60,onerror=redirect/https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/rhel/rh134/lab-schedule-tasks-in-red-hat-enterprise-linux/assets/20250624-08-48-37-qMTvtvj0.png)

现在，等待你已调度的作业执行。至少等待 5 分钟，以便所有作业都能完成。你可以检查你的 `at` 作业创建的文件是否存在以及是否包含预期的内容。

检查 `~/queue_g_job.txt`：

```bash
cat ~/queue_g_job.txt
```

你应该能看到一个日期和时间字符串。

检查 `~/batch_job.txt`：

```bash
cat ~/batch_job.txt
```

你应该能看到 "Batch job executed!"。

如果文件不存在或为空，则可能意味着作业尚未执行，或者命令存在问题。你可以重新检查 `atq` 以查看它们是否仍在待处理状态。

这完成了高级 `at` 作业管理步骤。剩余的 `at` 作业将在容器销毁时自动清理。

## 使用 'crontab' 调度用户定期作业

在本步骤中，你将学习如何为特定用户调度周期性任务，使用 `crontab`。与一次性运行的 `at` 任务不同，`cron` 任务会在指定的时间间隔内重复运行。这非常适合例行维护、数据备份或生成报告。

我们将继续在本地系统上学习用户 crontab 管理。

`crontab` 命令允许用户创建、编辑和查看他们自己的 `cron` 任务。每个用户都有自己的 `crontab` 文件。

要编辑你的 `crontab` 文件，请使用 `crontab -e` 命令。这将在默认文本编辑器（通常是 `vim`）中打开你的 `crontab` 文件。

```bash
crontab -e
```

**Vim 编辑器说明：**

- 按 `i` 进入插入模式（你会在底部看到 `-- INSERT --`）
- 使用箭头键导航
- 保存并退出：按 `Esc` 退出插入模式，然后输入 `:wq` 并按 `Enter`
- 不保存退出：按 `Esc`，然后输入 `:q!` 并按 `Enter`

在编辑器中，你将添加一个新行来定义你的 `cron` 任务。一个 `cron` 条目包含五个时间日期字段，后面是要执行的命令。这些字段是：

- **分钟 (0-59)**
- **小时 (0-23)**
- **日期 (1-31)**
- **月份 (1-12)**
- **星期几 (0-7，其中 0 或 7 代表星期日)**

你可以使用 `*` 作为通配符，表示某个字段的“每”，或者使用 `/` 来指定步长值（例如，`*/5` 表示每 5 分钟）。

让我们来调度一个任务，该任务每分钟将当前日期和时间追加到名为 `~/my_cron_log.txt` 的文件中。这将使我们能够快速观察到 `cron` 任务的运行情况。

在 vim 中按照以下步骤操作：

1. 按 `i` 进入插入模式
2. 向 `crontab` 文件添加以下行：

```
* * * * * /usr/bin/date >> ~/my_cron_log.txt
```

3. 按 `Esc` 退出插入模式
4. 输入 `:wq` 并按 `Enter` 保存并退出

你应该会看到一条消息，表明已安装新的 crontab：

```plaintext
crontab: installing new crontab
```

要验证你的 `cron` 任务是否已成功添加，你可以使用 `crontab -l` 命令列出你的 crontab 条目：

```bash
crontab -l
```

你应该会看到你刚刚添加的行：

```plaintext
* * * * * /usr/bin/date >> ~/my_cron_log.txt
```

现在，等待一两分钟，让 `cron` 任务至少运行一次。你可以查看当前时间，了解下一个分钟标记何时到来：

```bash
date
```

等待至少两分钟，让 cron 任务运行几次后，检查 `~/my_cron_log.txt` 文件的内容。

```bash
cat ~/my_cron_log.txt
```

你应该会看到一行或多行，每行包含一个日期和时间，表明你的 `cron` 任务已执行。

```plaintext
Mon Apr 8 10:30:01 AM EDT 2024
Mon Apr 8 10:31:01 AM EDT 2024
```

![Cron job output in log file](https://labex.io/cdn-cgi/image/format=auto,quality=60,onerror=redirect/https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/rhel/rh134/lab-schedule-tasks-in-red-hat-enterprise-linux/assets/20250624-08-52-40-IpAHQkr2.png)

这完成了用户 crontab 管理步骤。该 cron 任务将继续运行，直到容器被销毁。

## 管理用户 'crontab' 条目

在这一步中，你将学习更高级的管理用户 `crontab` 条目的技术，包括编辑现有作业、添加多个作业以及理解特殊的 `cron` 字符串。有效的 `crontab` 管理对于自动化例行任务至关重要。

我们将继续在本地系统上工作，以探索高级 crontab 管理技术。

让我们从添加一个新的 `cron` 作业开始。此作业将每两分钟将 "Hello from cron!" 追加到 `~/cron_messages.txt`。

打开你的 `crontab` 进行编辑：

```bash
crontab -e
```

在 vim 中：

1. 按 `i` 进入插入模式
2. 将以下行添加到 `crontab` 文件中：

```
*/2 * * * * echo "Hello from cron!" >> ~/cron_messages.txt
```

3. 按 `Esc` 退出插入模式
4. 键入 `:wq` 并按 `Enter` 保存并退出

验证条目是否已添加：

```bash
crontab -l
```

你应该看到新添加的行。

现在，让我们添加另一个 `cron` 作业，该作业每天早上 08:00 运行。此作业将把你的主目录的磁盘使用情况记录到 `~/disk_usage.log`。

再次打开你的 `crontab` 进行编辑：

```bash
crontab -e
```

在 vim 中：

1. 按 `i` 进入插入模式
2. 在上一行下方添加以下行：

```
0 8 * * * du -sh ~ >> ~/disk_usage.log
```

3. 按 `Esc` 退出插入模式
4. 键入 `:wq` 并按 `Enter` 保存并退出

验证两个条目都存在：

```bash
crontab -l
```

你现在应该看到列出的两个 `cron` 作业。

`cron` 还支持可以简化常见调度的特殊字符串。这些包括 `@reboot`、`@yearly`、`@annually`、`@monthly`、`@weekly`、`@daily`、`@midnight` 和 `@hourly`。例如，`@hourly` 等同于 `0 * * * *`。

让我们添加一个每小时运行并将系统运行时间记录到 `~/uptime_log.txt` 的作业。

打开你的 `crontab` 进行编辑：

```bash
crontab -e
```

在 vim 中：

1. 按 `i` 进入插入模式
2. 添加以下行：

```
@hourly uptime >> ~/uptime_log.txt
```

3. 按 `Esc` 退出插入模式
4. 键入 `:wq` 并按 `Enter` 保存并退出

验证所有三个条目：

```bash
crontab -l
```

你现在应该看到所有三个 `cron` 作业。

为了演示这些作业的效果，我们将等待一小段时间。由于作业以不同的间隔调度，我们不会立即看到它们全部执行，但我们可以验证设置。

等待至少 3 分钟，以允许 `*/2` 作业至少运行一次。

检查 `~/cron_messages.txt` 文件：

```bash
cat ~/cron_messages.txt
```

你应该看到至少一条 "Hello from cron!" 消息。

```plaintext
Hello from cron!
```

`~/disk_usage.log` 和 `~/uptime_log.txt` 文件可能尚未创建，具体取决于当前时间，因为它们分别被调度为每日和每小时执行。重要的是，它们的条目已在你的 `crontab` 中正确配置。

这完成了用户 crontab 管理步骤。所有 cron 作业将继续运行，直到容器被销毁。

## 使用 cron 目录调度系统定期作业

在这一步中，你将学习如何使用 `cron` 目录调度重复的系统范围任务。与特定于用户的用户 `crontab` 条目不同，系统 `cron` 作业由 root 用户管理，并影响整个系统。这些通常用于系统维护、日志轮换和其他管理任务。

我们将继续在本地系统上工作，以探索系统范围的 cron 作业配置。

系统范围的 `cron` 作业在 `/etc/crontab` 中定义，或者通过将脚本放置在特定目录中：

- `/etc/cron.hourly/`：此目录中的脚本每小时运行一次。
- `/etc/cron.daily/`：此目录中的脚本每天运行一次。
- `/etc/cron.weekly/`：此目录中的脚本每周运行一次。
- `/etc/cron.monthly/`：此目录中的脚本每月运行一次。

这些目录由 `run-parts` 实用程序处理，该实用程序执行它们中的所有可执行文件。

要管理系统 `cron` 作业，你需要 root 权限。由于 labex 用户具有 sudo 访问权限，我们可以使用 `sudo` 来执行所需的命令。

让我们创建一个简单的脚本，将消息记录到系统日志。我们将把这个脚本放在 `/etc/cron.hourly/` 中，使其每小时运行一次。

首先，创建脚本文件 `/etc/cron.hourly/my_hourly_script`：

```bash
sudo nano /etc/cron.hourly/my_hourly_script
```

将以下内容添加到文件中：

```bash
#!/bin/bash
logger "Hourly cron job executed at $(date)"
```

保存并退出编辑器（在 `nano` 中使用 `Ctrl+o`、`Enter`、`Ctrl+x`）。

接下来，你需要使脚本可执行。如果没有执行权限，`run-parts` 将忽略它。

```bash
sudo chmod +x /etc/cron.hourly/my_hourly_script
```

现在，让我们验证脚本是否可执行：

```bash
ls -l /etc/cron.hourly/my_hourly_script
```

你应该在权限中看到 `x`，例如：`-rwxr-xr-x`。

由于 `cron.hourly` 作业每小时运行一次，我们无法在这个实验中等待一整小时来验证它的执行。但是，我们可以手动触发 hourly 目录的 `run-parts` 命令来模拟它的执行。

```bash
sudo run-parts /etc/cron.hourly/
```

此命令将执行 `/etc/cron.hourly/` 中的所有可执行脚本。我们创建的脚本使用 `logger` 命令将消息写入系统日志。虽然我们无法在此容器环境中轻松验证日志输出，但重要的学习目标是了解如何在 cron 目录中创建和管理脚本。

在真实的 RHEL 系统中，你将能够使用 `journalctl` 或 `/var/log/messages` 检查系统日志，以验证脚本是否成功执行。

这完成了系统 cron 作业管理步骤。该脚本将保留在原位，并在真实系统环境中每小时执行一次。

## 配置 systemd 定时器以执行重复任务

在这一步中，你将了解 `systemd` 定时器，它是 `cron` 在 Linux 系统上调度任务的现代替代方案。`systemd` 定时器提供了更大的灵活性，并与 `systemd` 生态系统更好地集成。虽然 `systemctl` 命令通常用于管理 `systemd` 单元，但由于 Docker 容器环境的限制，我们将专注于直接创建和验证定时器和服务单元文件。

`systemd` 定时器与 `systemd` 服务单元结合使用。定时器单元（`.timer` 文件）定义了何时应该运行任务，而服务单元（`.service` 文件）定义了应该执行什么任务。

我们将继续在本地系统上工作，以探索 systemd 定时器配置。

你需要 root 权限才能在系统目录中创建 `systemd` 单元文件。由于 labex 用户具有 sudo 访问权限，我们可以使用 `sudo` 来执行所需的命令。

让我们创建一个简单的服务，将消息记录到文件中。我们将把这个服务单元文件放在 `/etc/systemd/system/` 中，这是自定义服务单元的典型存储位置。

创建服务单元文件 `/etc/systemd/system/my-custom-task.service`：

```bash
sudo nano /etc/systemd/system/my-custom-task.service
```

将以下内容添加到文件中：

```ini
[Unit]
Description=My Custom Scheduled Task

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'echo "My custom task executed at $(date)" >> /var/log/my-custom-task.log'
```

保存并退出编辑器（在 `nano` 中使用 `Ctrl+o`、`Enter`、`Ctrl+x`）。

接下来，创建定时器单元文件 `/etc/systemd/system/my-custom-task.timer`。此定时器将每 5 分钟激活我们的服务。

```bash
sudo nano /etc/systemd/system/my-custom-task.timer
```

将以下内容添加到文件中：

```ini
[Unit]
Description=Run My Custom Scheduled Task every 5 minutes

[Timer]
OnCalendar=*:0/5
Persistent=true

[Install]
WantedBy=timers.target
```

保存并退出编辑器。

**`OnCalendar` 的解释：**

- `*:0/5` 意思是“每 5 分钟”。
    - `*` 代表年、月、日、小时（任何值）。
    - `0/5` 代表分钟，意思是从第 0 分钟开始，每 5 分钟（0、5、10、...、55）。

在典型的 `systemd` 环境中，你现在将运行 `systemctl daemon-reload` 以使 `systemd` 知道新的单元文件，然后运行 `systemctl enable --now my-custom-task.timer` 以启动定时器。但是，由于 Docker 容器的限制，`systemctl` 无法完全运行。

相反，我们将手动验证文件的创建。容器内的 `systemd` 守护程序最终可能会拾取这些文件，但我们无法在此实验设置中直接控制或观察其定时器执行。这里的主要目标是了解如何_配置_这些文件。

让我们验证已创建文件的存在：

```bash
ls -l /etc/systemd/system/my-custom-task.service
ls -l /etc/systemd/system/my-custom-task.timer
```

你应该看到输出，表明这两个文件都存在。

要模拟服务的执行，你可以手动运行在 `ExecStart` 中定义的命令：

```bash
sudo /bin/bash -c 'echo "My custom task executed at $(date)" >> /var/log/my-custom-task.log'
```

现在，检查日志文件以查看输出：

```bash
sudo cat /var/log/my-custom-task.log
```

你应该看到你刚刚记录的消息：

```plaintext
My custom task executed at Tue Jun 10 06:54:40 UTC 2025
```

这完成了 systemd 定时器配置步骤。服务和定时器单元文件将保留在原位以供参考。

## 使用 systemd-tmpfiles 管理临时文件

在这一步中，你将学习如何使用 `systemd-tmpfiles` 管理临时文件和目录。此实用程序是 `systemd` 的一部分，负责创建、删除和清理易失性和临时文件和目录。它通常用于管理 `/tmp`、`/var/tmp` 和其他临时存储位置，确保定期删除旧文件。

我们将继续在本地系统上工作，以探索 systemd-tmpfiles 配置。

你需要 root 权限才能配置 `systemd-tmpfiles`。由于 labex 用户具有 sudo 访问权限，我们可以使用 `sudo` 来执行所需的命令。

`systemd-tmpfiles` 从 `/etc/tmpfiles.d/` 和 `/usr/lib/tmpfiles.d/` 读取配置文件。这些文件定义了创建、删除和管理文件和目录的规则。

让我们创建一个自定义配置文件来管理一个新的临时目录。我们将创建一个目录 `/run/my_temp_dir`，并配置 `systemd-tmpfiles` 从其中清理超过 1 分钟的文件。

创建配置文件 `/etc/tmpfiles.d/my_temp_dir.conf`：

```bash
sudo nano /etc/tmpfiles.d/my_temp_dir.conf
```

将以下内容添加到文件中：

```
d /run/my_temp_dir 0755 labex labex 1m
```

**对该行的解释：**

- `d`：指定此条目定义一个目录。
- `/run/my_temp_dir`：目录的路径。
- `0755`：目录的权限。
- `labex labex`：目录的所有者和组。
- `1m`：此目录中的文件应该被删除的时间（1 分钟）。

保存并退出编辑器（在 `nano` 中使用 `Ctrl+o`、`Enter`、`Ctrl+x`）。

现在，让我们告诉 `systemd-tmpfiles` 应用此配置。`--create` 选项将在目录不存在时创建它。

```bash
sudo systemd-tmpfiles --create /etc/tmpfiles.d/my_temp_dir.conf
```

验证目录是否已使用正确的权限和所有权创建：

```bash
ls -ld /run/my_temp_dir
```

你应该看到类似以下的输出：

```plaintext
drwxr-xr-x 2 labex labex 6 Jun 10 06:55 /run/my_temp_dir
```

接下来，让我们在这个新的临时目录中创建一个测试文件：

```bash
sudo touch /run/my_temp_dir/test_file.txt
```

验证文件是否存在：

```bash
ls -l /run/my_temp_dir/test_file.txt
```

现在，我们需要等待超过 1 分钟，让文件根据我们的配置“变旧”。等待至少 70 秒（1 分钟 10 秒）。

等待超过 1 分钟后，我们将手动运行 `systemd-tmpfiles`，并使用 `--clean` 选项来触发基于我们配置的清理过程。

```bash
sudo systemd-tmpfiles --clean /etc/tmpfiles.d/my_temp_dir.conf
```

最后，检查 `test_file.txt` 是否已被删除：

```bash
ls -l /run/my_temp_dir/test_file.txt
```

你应该得到一个“没有这样的文件或目录”错误，这表明 `systemd-tmpfiles` 成功地清理了旧文件。

这完成了 systemd-tmpfiles 配置步骤。配置文件和临时目录将保留在原位以供参考。

## 总结

在这个实验中，你学习了如何使用 `at` 命令调度和管理一次性任务，包括以交互和非交互方式调度作业，使用 `atq` 查看 `at` 队列，以及使用 `atrm` 删除待处理的作业。你还掌握了使用 `crontab` 调度用户特定的重复任务的技能，涵盖了如何编辑、列出和删除 cron 作业，以及理解用于指定执行时间的 cron 语法。此外，该实验演示了如何通过将脚本放置在标准的 cron 目录（`/etc/cron.hourly`、`/etc/cron.daily` 等）中来调度系统范围的重复任务，以及如何在 `/etc/cron.d` 中创建自定义 cron 作业。

最后，你探索了使用 `systemd` 定时器进行高级任务调度，学习了如何创建和启用服务和定时器单元以执行重复任务，以及如何使用 `systemd-tmpfiles` 管理临时文件和目录以进行自动清理。这个综合实验提供了在 RHEL 系统上管理各种任务调度需求的实践经验，从简单的单次命令到复杂的重复系统进程。