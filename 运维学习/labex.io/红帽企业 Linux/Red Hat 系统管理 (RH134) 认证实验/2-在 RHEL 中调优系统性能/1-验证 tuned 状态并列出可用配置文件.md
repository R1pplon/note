# 验证 tuned 状态并列出可用配置文件

在这一步，你将学习如何验证 `tuned` 守护进程的状态，并列出 RHEL 系统上可用的调优配置文件。`tuned` 是一个动态自适应系统调优守护进程，它会调整系统设置，以优化特定工作负载的性能。它使用调优配置文件来应用一组系统范围的设置。

1. **验证 `tuned` 守护进程是否正在运行**。
    在这个容器环境中，我们将通过查找 `tuned` 进程来检查 `tuned` 守护进程是否正在运行。我们还可以通过检查它是否响应命令来验证其功能。

    首先，检查 `tuned` 进程是否正在运行：

    ```bash
    pgrep tuned
    ```

    如果 `tuned` 正在运行，此命令将返回其进程 ID（PID）。如果没有返回 PID，你可以手动启动守护进程：

    ```bash
    sudo /usr/sbin/tuned &
    ```

    然后验证它是否正在运行：

    ```bash
    pgrep tuned
    ```

    你应该看到类似如下的输出：

    ```plaintext
    739
    ```

    *(注意：你的输出中的 PID 值会有所不同。)*

    此外，你可以通过检查 `tuned` 是否响应状态查询来验证其是否正常工作：

    ```bash
    sudo tuned-adm active
    ```

    这应该会返回当前活动的配置文件，且没有错误。

2. **列出可用的调优配置文件并识别活动的配置文件**。
    `tuned-adm list` 命令显示所有可用的调优配置文件，并突出显示当前活动的配置文件。

    ```bash
    sudo tuned-adm list
    ```

    系统将提示你输入密码。注意输出中的 `Current active profile`。

    ```plaintext
    Available profiles:
    - accelerator-performance     - Throughput performance based tuning with disabled higher latency STOP states
    - aws                         - Optimize for aws ec2 instances
    - balanced                    - General non-specialized tuned profile
    - balanced-battery            - Balanced profile biased towards power savings changes for battery
    - desktop                     - Optimize for the desktop use-case
    - hpc-compute                 - Optimize for HPC compute workloads
    - intel-sst                   - Configure for Intel Speed Select Base Frequency
    - latency-performance         - Optimize for deterministic performance at the cost of increased power consumption
    - network-latency             - Optimize for deterministic performance at the cost of increased power consumption, focused on low latency network performance
    - network-throughput          - Optimize for streaming network throughput, generally only necessary on older CPUs or 40G+ networks
    - optimize-serial-console     - Optimize for serial console use.
    - powersave                   - Optimize for low power consumption
    - throughput-performance      - Broadly applicable tuning that provides excellent performance across a variety of common server workloads
    - virtual-guest               - Optimize for running inside a virtual guest
    - virtual-host                - Optimize for running KVM guests
    Current active profile: virtual-guest
    ```

3. **查看 `virtual-guest` 配置文件**。
    `virtual-guest` 配置文件通常是虚拟机的默认配置文件。你可以检查其配置文件，以了解它应用了哪些设置。

    ```bash
    cat /usr/lib/tuned/virtual-guest/tuned.conf
    ```

    此命令显示 `virtual-guest` 配置文件的 `tuned` 配置，包括它从其他配置文件继承的参数。

    ```plaintext
    #
    # tuned configuration
    #

    [main]
    summary=Optimize for running inside a virtual guest
    include=throughput-performance

    [vm]
    # If a workload mostly uses anonymous memory and it hits this limit, the entire
    # working set is buffered for I/O, and any more write buffering would require
    # swapping, so it's time to throttle writes until I/O can catch up.  Workloads
    # that mostly use file mappings may be able to use even higher values.
    #
    # The generator of dirty data starts writeback at this percentage (system default
    # is 20%)
    dirty_ratio = 30

    [sysctl]
    # Filesystem I/O is usually much more efficient than swapping, so try to keep
    # swapping low.  It's usually safe to go even lower than this on systems with
    # server-grade storage.
    vm.swappiness = 30
    ```

4. **验证是否应用了 `vm.dirty_background_ratio` 参数**。
    `virtual-guest` 配置文件包含 `throughput-performance`。让我们检查一个 `throughput-performance` 通常设置的参数，例如 `vm.dirty_background_ratio`。此参数控制系统何时开始在后台将脏页写入磁盘。

    ```bash
    sysctl vm.dirty_background_ratio
    ```

    输出将显示此内核参数的当前值。

    ```plaintext
    vm.dirty_background_ratio = 10
    ```
