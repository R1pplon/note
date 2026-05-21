# 在 RHEL 上使用 nice 和 renice 调整进程优先级

在这一步，你将学习如何使用 `nice` 和 `renice` 命令影响进程的调度优先级。进程的 `nice` 值（也称为 niceness）表示其对 Linux 调度程序的优先级。较低的 `nice` 值（更负数）表示较高的优先级，而较高的 `nice` 值（更正数）表示较低的优先级。`nice` 值的范围通常从 -20（最高优先级）到 19（最低优先级），默认值为 0。

1. **启动多个 `sha1sum /dev/zero &` 实例，然后启动一个 `nice` 级别为 10 的附加实例**。
    我们将启动几个 `sha1sum` 进程来模拟繁忙的系统。然后，我们将启动一个具有故意较低优先级（较高 `nice` 值）的进程来观察效果。

    首先，启动三个常规实例（如果需要，根据你的 CPU 核心数进行调整，但至少要与虚拟处理器一样多以创建争用）：

    ```bash
    for i in {1..3}; do sha1sum /dev/zero & done
    ```

    接下来，启动第四个实例，其 `nice` 级别为 10。与其他人相比，此进程将具有较低的优先级。

    ```bash
    nice -n 10 sha1sum /dev/zero &
    ```

    你将看到类似于此的输出，指示后台进程的 PID：

    ```plaintext
    [1] 5443
    [2] 5444
    [3] 5445
    [4] 5446
    ```

    *(注意：你的输出中的 PID 值将有所不同。)*

2. **使用 `ps` 和 `pgrep` 命令显示每个进程的 PID、CPU 使用率百分比、`nice` 值和可执行文件名**。
    观察 `%CPU` 和 `NI` 列。`nice` 值为 10 的实例应显示比其他实例更低的 CPU 使用率百分比，因为调度程序为其分配的 CPU 时间较少。

    ```bash
    ps -o pid,pcpu,nice,comm $(pgrep sha1sum)
    ```

    查找 `NI` 值为 `10` 的进程。它的 `%CPU` 应该明显低于其他进程。

    ```plaintext
        PID %CPU  NI COMMAND
       5443 56.8   0 sha1sum
       5444 58.0   0 sha1sum
       5445 56.5   0 sha1sum
       5446  6.7  10 sha1sum
    ```

    _(注意：确切的 `%CPU` 值将根据系统负载和核心数而变化，但 `nice 10` 的进程应该具有较低的份额。)_

3. **使用 `sudo renice` 命令将其中一个常规进程的 `nice` 级别更改为 5**。
    `renice` 命令允许你更改已运行进程的 `nice` 值。我们将通过将其中一个常规进程（nice 值为 0）更改为 nice 值为 5 来演示这一点。

    首先，从上一个 `ps` 命令的输出中识别 `sha1sum` 进程之一的 PID，该进程的 `nice` 值为 0。让我们使用上面示例中的第一个进程（PID 5443）。

    ```bash
    sudo renice -n 5 <PID_of_regular_process>
    ```

    将 `<PID_of_regular_process>` 替换为你标识的实际 PID。例如：

    ```bash
    sudo renice -n 5 5443
    ```

    你应该看到确认优先级更改的输出：

    ```plaintext
    5443 (process ID) old priority 0, new priority 5
    ```

4. **重复 `ps` 和 `pgrep` 命令以显示 CPU 百分比和 `nice` 级别**。
    观察你修改其 `nice` 值的进程的 CPU 使用率变化。与 nice 值为 0 的进程相比，nice 值为 5 的进程现在应该具有略低的 CPU 使用率，但高于 nice 值为 10 的进程。

    ```bash
    ps -o pid,pcpu,nice,comm $(pgrep sha1sum)
    ```

    你应该看到已修改进程的 `NI` 值现在为 `5`，并且其 CPU 使用率反映了其新的优先级级别。

    ```plaintext
        PID %CPU  NI COMMAND
       5443 55.4   5 sha1sum
       5444 67.2   0 sha1sum
       5445 67.1   0 sha1sum
       5446  7.5  10 sha1sum
    ```

    _(注意：确切的 `%CPU` 值将有所不同，但你应该观察到具有较低 nice 值（较高优先级）的进程获得更多 CPU 时间。)_
