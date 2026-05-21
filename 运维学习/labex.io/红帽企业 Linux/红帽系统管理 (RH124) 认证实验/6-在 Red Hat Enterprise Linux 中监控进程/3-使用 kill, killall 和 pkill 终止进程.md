# 使用 kill, killall 和 pkill 终止进程

在本步骤中，你将学习如何使用 `kill`、`killall` 和 `pkill` 命令终止进程。这些命令对于管理系统资源和停止不当行为的应用程序至关重要。

Linux 中的进程会响应信号。信号是一种发送给进程的软件中断。不同的信号具有不同的含义，例如终止进程、暂停进程或使其重新加载其配置。

首先，让我们了解一些基本的进程管理信号：

- **SIGTERM (15)**: `kill` 命令默认发送的信号。这是一个“礼貌”的终止请求。进程可以捕获此信号，清理自身，然后退出。
- **SIGKILL (9)**: 一种“不可阻止”的信号，强制立即终止。进程无法忽略或处理此信号。仅在必要时使用。
- **SIGHUP (1)**: 通常用于指示进程重新加载其配置文件，而无需重新启动。
- **SIGINT (2)**: 通过按下 `Ctrl+C` 发送，通常用于中断前台进程。
- **SIGSTOP (19)**: 暂停进程。它无法被阻止或处理。
- **SIGCONT (18)**: 恢复已暂停的进程。

你可以使用 `kill -l` 列出所有可用信号及其编号：

```bash
kill -l
```

你将看到类似这样的信号列表：

```plaintext
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
...output omitted...
```

### 使用 `kill`

`kill` 命令将指定信号发送到由其进程 ID (PID) 标识的进程。

让我们创建一些后台进程来练习终止它们。我们将再次使用 `sleep` 命令。

```bash
sleep 300 &
sleep 301 &
sleep 302 &
```

现在，使用 `jobs` 查看它们的作业号和 PID：

```bash
jobs
```

```plaintext
[1] 1234
[2] 1235
[3] 1236
```

(注意：你的 PID 会有所不同。)

让我们找到第一个 `sleep` 进程的 PID。你可以使用 `ps aux | grep sleep` 并查找与 `sleep 300` 相关的 PID。

```bash
ps aux | grep sleep
```

你将看到类似这样的输出。确定 `sleep 300` 的 PID。例如，如果 PID 是 `1234`：

```plaintext
labex       1234  0.0  0.0   2200   680 pts/0    S    HH:MM   0:00 sleep 300
labex       1235  0.0  0.0   2200   680 pts/0    S    HH:MM   0:00 sleep 301
labex       1236  0.0  0.0   2200   680 pts/0    S    HH:MM   0:00 sleep 302
labex       1237  0.0  0.0   6000  1000 pts/0    S+   HH:MM   0:00 grep sleep
```

要使用默认的 `SIGTERM` 信号终止 `sleep 300`，请使用 `kill` 后跟其 PID。将 `1234` 替换为实际找到的 PID：

```bash
kill 1234
```

你可能会看到一条消息，例如 `[1]+ Terminated sleep 300`。使用 `jobs` 或 `ps aux | grep sleep` 验证它已消失：

```bash
jobs
```

```plaintext
[2]- Running    sleep 301 &
[3]+ Running    sleep 302 &
```

现在，让我们使用 `SIGKILL` 强制终止 `sleep 301`。找到其 PID（例如，`1235`）并使用 `kill -9` 或 `kill -SIGKILL`：

```bash
kill -9 1235
```

你可能会看到 `[2]- Killed sleep 301`。再次验证：

```bash
jobs
```

```plaintext
[3]+ Running    sleep 302 &
```

### 使用 `killall`

`killall` 命令通过进程名称而不是 PID 来终止进程。它会向所有与指定命令名称匹配的进程发送信号。

让我们创建一些额外的 `sleep` 进程：

```bash
sleep 303 &
sleep 304 &
sleep 305 &
```

验证它们是否正在运行：

```bash
jobs
```

```plaintext
[3] Running    sleep 302 &
[4] Running    sleep 303 &
[5] Running    sleep 304 &
[6] Running    sleep 305 &
```

现在，使用 `killall` 终止所有 `sleep` 进程。默认情况下，`killall` 发送 `SIGTERM`。

```bash
killall sleep
```

你将看到每个终止的 `sleep` 进程的消息。验证所有 `sleep` 进程是否已消失：

```bash
jobs
```

```plaintext
(no output)
```

### 使用 `pkill`

`pkill` 命令类似于 `killall`，但它提供了更高级的选择标准，包括命令名称、用户 ID、组 ID 和控制终端的模式匹配。它非常适合针对特定进程集。

让我们为 `pkill` 创建一些新的 `sleep` 进程：

```bash
sleep 306 &
sleep 307 &
sleep 308 &
```

验证它们是否正在运行：

```bash
jobs
```

```plaintext
[1] Running    sleep 306 &
[2] Running    sleep 307 &
[3] Running    sleep 308 &
```

要终止当前用户（`labex`）拥有的所有 `sleep` 进程，可以使用 `pkill -u labex sleep`：

```bash
pkill -u labex sleep
```

此命令将终止属于 `labex` 用户的所有 `sleep` 进程。

验证所有 `sleep` 进程是否已消失：

```bash
jobs
```

```plaintext
(no output)
```

你还可以使用 `pkill` 与模式一起使用。例如，如果你有名为 `my_app_v1` 和 `my_app_v2` 的进程，你可以使用 `pkill my_app` 终止两者。

这些命令提供了灵活的方式来管理和终止进程，从通过 PID 针对单个进程到基于名称或其他属性终止多个进程。在使用 `kill -9` 或 `SIGKILL` 时，请务必谨慎，因为它可能会导致数据丢失，如果进程没有机会清理。
