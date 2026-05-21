# 使用 `renice` 调整运行中进程的优先级

有时你需要更改已经在运行的进程的优先级。为此，你可以使用 `renice` 命令。与 `nice` 类似，你需要 `sudo` 权限来提高进程的优先级（设置更小或更负的谦让度值）。要更改其他用户（如 `root`）拥有的进程优先级，同样需要 `sudo` 权限。

## 任务

- 找到你在上一步中启动的 `sleep 600` 进程的进程 ID（PID）。
- 使用 `renice` 命令将其优先级调低，将谦让度值设为 `5`。
- 使用 `ps` 命令验证新的优先级级别。

## 要求

- 目标进程是上一步中的 `sleep 600` 进程。
- 新的谦让度值必须为 `5`。
- 你必须使用 `sudo` 来更改该进程的优先级（因为它是以 `sudo` 启动且由 `root` 拥有的）。

## 示例

运行 `renice` 命令后，`ps -e -o pid,ni,comm | grep sleep` 的输出应显示 `sleep` 进程的新 NI 值为 `5`。

```bash
ps -e -o pid,ni,comm | grep sleep
```

```plaintext
   2250   5 sleep
```

你也可以使用 `ps -e -o pid,ni,comm` 来查看所有进程：

```plaintext
  PID  NI COMMAND
    1   0 systemd
 2188   0 bash
 2250   5 sleep
```
