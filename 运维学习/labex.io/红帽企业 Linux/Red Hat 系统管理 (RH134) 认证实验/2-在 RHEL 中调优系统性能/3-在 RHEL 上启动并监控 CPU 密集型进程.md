# 在 RHEL 上启动并监控 CPU 密集型进程

在这一步，你将学习如何启动 CPU 密集型进程并监控它们的资源使用情况。这对于理解进程如何消耗系统资源以及如何识别瓶颈至关重要。我们将使用 `sha1sum /dev/zero` 命令，该命令持续计算无限的零流的 SHA1 校验和，从而有效地消耗 CPU 周期。

**重要提示：** 本练习使用在设备文件上执行无限校验和的命令，有意消耗大量的 CPU 资源。**在离开本练习或进入下一个实验之前，你必须终止所有练习进程。**

1. **确定系统上的 CPU 核心数量**。
    了解 CPU 核心的数量有助于你决定要运行多少个 CPU 密集型进程以充分利用系统。你可以在 `/proc/cpuinfo` 中找到此信息。

    ```bash
    grep -c '^processor' /proc/cpuinfo
    ```

    此命令计算以 `processor` 开头的行数，这对应于逻辑 CPU 核心（或虚拟处理器）的数量。

    ```plaintext
    2
    ```

    *(注意：你的输出可能会显示不同的核心数量，具体取决于系统的配置。)*

2. **为每个 CPU 核心启动两个 `sha1sum /dev/zero &` 命令的实例**。
    为了模拟高负载系统，我们将启动多个 `sha1sum /dev/zero &` 的实例。命令末尾的 `&` 在后台运行该进程，允许你继续使用终端。例如，如果你有 2 个 CPU 核心，你将启动 4 个实例（2 个实例/核心 \* 2 个核心）。

    ```bash
    for i in $(seq 1 $(grep -c '^processor' /proc/cpuinfo | awk '{print $1 * 2}')); do sha1sum /dev/zero & done
    ```

    此命令根据你的 CPU 核心数动态计算要启动的进程数。

    ```plaintext
    [1] 1234
    [2] 1235
    [3] 1236
    [4] 1237
    ```

    *(注意：你的输出中的 PID 值将与示例不同。)*

3. **验证后台作业是否正在运行**。
    `jobs` 命令列出当前从你的 shell 会话在后台运行的所有进程。

    ```bash
    jobs
    ```

    你应该看到你刚刚启动的 `sha1sum` 进程的列表。

    ```plaintext
    [1]   Running                 sha1sum /dev/zero &
    [2]   Running                 sha1sum /dev/zero &
    [3]   Running                 sha1sum /dev/zero &
    [4]-  Running                 sha1sum /dev/zero &
    ```

4. **使用 `ps` 和 `pgrep` 命令显示每个 `sha1sum` 进程的 CPU 使用率百分比**。
    `ps` 命令报告当前进程的快照。我们将把它与 `pgrep` 结合使用，以过滤 `sha1sum` 进程。

    - `ps -o pid,pcpu,nice,comm`：这指定输出格式：进程 ID（`pid`）、CPU 使用率百分比（`pcpu`）、`nice` 值（`nice`）和命令名称（`comm`）。
    - `$(pgrep sha1sum)`：此命令替换查找所有名为 `sha1sum` 的进程的 PID，并将它们作为参数传递给 `ps`。

    ```bash
    ps -o pid,pcpu,nice,comm $(pgrep sha1sum)
    ```

    你应该看到每个 `sha1sum` 进程消耗了大量的 CPU 百分比。

    ```plaintext
        PID %CPU  NI COMMAND
       5248 48.8   0 sha1sum
       5249 48.7   0 sha1sum
       5250 48.8   0 sha1sum
       5251 48.8   0 sha1sum
    ```

    _(注意：`%CPU` 值可能会波动，但应该很高，表明 CPU 使用率很高。`NI` 列显示 nice 值。)_

5. **终止所有正在运行的 `sha1sum` 进程并验证没有剩余进程**。
    在继续之前清理这些 CPU 密集型进程至关重要。`pkill` 命令根据进程名称终止进程。

    ```bash
    pkill sha1sum
    ```

    现在，验证后台没有运行任何 `sha1sum` 作业。

    ```bash
    jobs
    ```

    输出应该为空，或者指示所有作业已终止。

    ```plaintext
    [1]   Terminated              sha1sum /dev/zero
    [2]   Terminated              sha1sum /dev/zero
    [3]   Terminated              sha1sum /dev/zero
    [4]-  Terminated              sha1sum /dev/zero
    ```

    *(注意：你可能会看到“已终止”消息，这是预期的，因为进程正在停止。)*
