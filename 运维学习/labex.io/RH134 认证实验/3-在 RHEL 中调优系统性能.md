## 介绍

在这个实验（Lab）中，你将学习如何使用 `tuned` 优化 RHEL 系统的性能，以及如何使用 `nice` 和 `renice` 管理进程优先级。你将首先验证 `tuned` 的安装并列出可用的配置文件，然后观察更改 `tuned` 配置文件如何影响系统参数。

这个实验（Lab）将引导你启动和监控 CPU 密集型进程，然后使用 `nice` 和 `renice` 调整它们的优先级，以了解它们对资源分配的影响。最后，你将学习如何清理正在运行的进程，确保你完全理解在 RHEL 上进行性能调优。

## 验证 tuned 状态并列出可用配置文件

在这一步，你将学习如何验证 `tuned` 守护进程的状态，并列出 RHEL 系统上可用的调优配置文件。`tuned` 是一个动态自适应系统调优守护进程，它会调整系统设置，以优化特定工作负载的性能。它使用调优配置文件来应用一组系统范围的设置。

1. **验证 `tuned` 守护进程是否正在运行**。  
    在这个容器环境中，我们将通过查找 `tuned` 进程来检查 `tuned` 守护进程是否正在运行。我们还可以通过检查它是否响应命令来验证其功能。
    
    首先，检查 `tuned` 进程是否正在运行：
    
    ```bash
    pgrep tuned
    ```
    
    如果 `tuned` 正在运行，此命令将返回其进程 ID（PID）。如果没有返回 PID，你可以手动启动守护进程：
    
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
    
    _(注意：你的输出中的 PID 值会有所不同。)_
    
    此外，你可以通过检查 `tuned` 是否响应状态查询来验证其是否正常工作：
    
    ```bash
    sudo tuned-adm active
    ```
    
    这应该会返回当前活动的配置文件，且没有错误。
    
2. **列出可用的调优配置文件并识别活动的配置文件**。  
    `tuned-adm list` 命令显示所有可用的调优配置文件，并突出显示当前活动的配置文件。
    
    ```bash
    sudo tuned-adm list
    ```
    
    系统将提示你输入密码。注意输出中的 `Current active profile`。
    
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
    
3. **查看 `virtual-guest` 配置文件**。  
    `virtual-guest` 配置文件通常是虚拟机的默认配置文件。你可以检查其配置文件，以了解它应用了哪些设置。
    
    ```bash
    cat /usr/lib/tuned/virtual-guest/tuned.conf
    ```
    
    此命令显示 `virtual-guest` 配置文件的 `tuned` 配置，包括它从其他配置文件继承的参数。
    
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
    
4. **验证是否应用了 `vm.dirty_background_ratio` 参数**。  
    `virtual-guest` 配置文件包含 `throughput-performance`。让我们检查一个 `throughput-performance` 通常设置的参数，例如 `vm.dirty_background_ratio`。此参数控制系统何时开始在后台将脏页写入磁盘。
    
    ```bash
    sysctl vm.dirty_background_ratio
    ```
    
    输出将显示此内核参数的当前值。
    
    ```plaintext
    vm.dirty_background_ratio = 10
    ```

## 更改 tuned 配置文件并观察系统参数变化

在这一步，你将学习如何更改活动的 `tuned` 配置文件，并观察对系统参数的即时影响。更改 `tuned` 配置文件允许你快速应用一组针对不同工作负载（例如，吞吐量密集型任务或节能）量身定制的性能优化。

1. **将当前活动调优配置文件更改为 `throughput-performance`**。  
    `throughput-performance` 配置文件专为需要高吞吐量的系统而设计，通常会牺牲一些延迟。它通常针对磁盘 I/O 和网络性能进行优化。使用 `tuned-adm profile` 命令切换配置文件。
    
    ```bash
    sudo tuned-adm profile throughput-performance
    ```
    
    系统将提示你输入密码。
    
    ```plaintext
    $ sudo tuned-adm profile throughput-performance
    [sudo] password for user:
    ```
    
2. **确认新的活动配置文件**。  
    更改配置文件后，最好验证新的配置文件确实处于活动状态。你可以使用 `tuned-adm active` 来执行此操作。
    
    ```bash
    sudo tuned-adm active
    ```
    
    输出现在应该显示 `throughput-performance` 作为活动配置文件。
    
    ```plaintext
    Current active profile: throughput-performance
    ```
    
3. **验证 `vm.dirty_ratio` 和 `vm.swappiness` 参数是否已更改**。  
    `throughput-performance` 配置文件修改与内存管理相关的内核参数，例如 `vm.dirty_ratio` 和 `vm.swappiness`。即使 `virtual-guest` 配置文件继承自 `throughput-performance`，直接切换到 `throughput-performance` 配置文件也会应用基本值，而不会进行 virtual-guest 特定的修改。
    
    - `vm.dirty_ratio`：此参数定义系统内存中可以被脏页（已修改但尚未写入磁盘的页面）填充的最大百分比，然后系统开始将它们写入磁盘。较高的值可以通过允许在内存中缓冲更多数据来提高吞吐量。
    - `vm.swappiness`：此参数控制内核将匿名内存（应用程序数据）从 RAM 换出到交换空间的积极程度。较低的值意味着内核将尝试在 RAM 中保留更多应用程序数据，这通常对性能更好。
    
    让我们使用 `sysctl` 检查它们的当前值。
    
    ```bash
    sysctl vm.dirty_ratio
    sysctl vm.swappiness
    ```
    
    你应该观察到这些值已从 `virtual-guest` 配置文件设置（dirty_ratio = 30，vm.swappiness = 30）更改为基本 `throughput-performance` 配置文件值：
    
    ```plaintext
    vm.dirty_ratio = 40
    vm.swappiness = 10
    ```
    
    _(注意：这些值反映了基本 throughput-performance 优化，没有 virtual-guest 特定的修改。)_

## 在 RHEL 上启动并监控 CPU 密集型进程

在这一步，你将学习如何启动 CPU 密集型进程并监控它们的资源使用情况。这对于理解进程如何消耗系统资源以及如何识别瓶颈至关重要。我们将使用 `sha1sum /dev/zero` 命令，该命令持续计算无限的零流的 SHA1 校验和，从而有效地消耗 CPU 周期。

**重要提示：** 本练习使用在设备文件上执行无限校验和的命令，有意消耗大量的 CPU 资源。**在离开本练习或进入下一个实验之前，你必须终止所有练习进程。**

1. **确定系统上的 CPU 核心数量**。  
    了解 CPU 核心的数量有助于你决定要运行多少个 CPU 密集型进程以充分利用系统。你可以在 `/proc/cpuinfo` 中找到此信息。
    
    ```bash
    grep -c '^processor' /proc/cpuinfo
    ```
    
    此命令计算以 `processor` 开头的行数，这对应于逻辑 CPU 核心（或虚拟处理器）的数量。
    
    ```plaintext
    2
    ```
    
    _(注意：你的输出可能会显示不同的核心数量，具体取决于系统的配置。)_
    
2. **为每个 CPU 核心启动两个 `sha1sum /dev/zero &` 命令的实例**。  
    为了模拟高负载系统，我们将启动多个 `sha1sum /dev/zero &` 的实例。命令末尾的 `&` 在后台运行该进程，允许你继续使用终端。例如，如果你有 2 个 CPU 核心，你将启动 4 个实例（2 个实例/核心 * 2 个核心）。
    
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
    
    _(注意：你的输出中的 PID 值将与示例不同。)_
    
3. **验证后台作业是否正在运行**。  
    `jobs` 命令列出当前从你的 shell 会话在后台运行的所有进程。
    
    ```bash
    jobs
    ```
    
    你应该看到你刚刚启动的 `sha1sum` 进程的列表。
    
    ```plaintext
    [1]   Running                 sha1sum /dev/zero &
    [2]   Running                 sha1sum /dev/zero &
    [3]   Running                 sha1sum /dev/zero &
    [4]-  Running                 sha1sum /dev/zero &
    ```
    
4. **使用 `ps` 和 `pgrep` 命令显示每个 `sha1sum` 进程的 CPU 使用率百分比**。  
    `ps` 命令报告当前进程的快照。我们将把它与 `pgrep` 结合使用，以过滤 `sha1sum` 进程。
    
    - `ps -o pid,pcpu,nice,comm`：这指定输出格式：进程 ID（`pid`）、CPU 使用率百分比（`pcpu`）、`nice` 值（`nice`）和命令名称（`comm`）。
    - `$(pgrep sha1sum)`：此命令替换查找所有名为 `sha1sum` 的进程的 PID，并将它们作为参数传递给 `ps`。
    
    ```bash
    ps -o pid,pcpu,nice,comm $(pgrep sha1sum)
    ```
    
    你应该看到每个 `sha1sum` 进程消耗了大量的 CPU 百分比。
    
    ```plaintext
        PID %CPU  NI COMMAND
       5248 48.8   0 sha1sum
       5249 48.7   0 sha1sum
       5250 48.8   0 sha1sum
       5251 48.8   0 sha1sum
    ```
    
    _(注意：`%CPU` 值可能会波动，但应该很高，表明 CPU 使用率很高。`NI` 列显示 nice 值。)_
    
5. **终止所有正在运行的 `sha1sum` 进程并验证没有剩余进程**。  
    在继续之前清理这些 CPU 密集型进程至关重要。`pkill` 命令根据进程名称终止进程。
    
    ```bash
    pkill sha1sum
    ```
    
    现在，验证后台没有运行任何 `sha1sum` 作业。
    
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
    
    _(注意：你可能会看到“已终止”消息，这是预期的，因为进程正在停止。)_

## 在 RHEL 上使用 nice 和 renice 调整进程优先级

在这一步，你将学习如何使用 `nice` 和 `renice` 命令影响进程的调度优先级。进程的 `nice` 值（也称为 niceness）表示其对 Linux 调度程序的优先级。较低的 `nice` 值（更负数）表示较高的优先级，而较高的 `nice` 值（更正数）表示较低的优先级。`nice` 值的范围通常从 -20（最高优先级）到 19（最低优先级），默认值为 0。

1. **启动多个 `sha1sum /dev/zero &` 实例，然后启动一个 `nice` 级别为 10 的附加实例**。  
    我们将启动几个 `sha1sum` 进程来模拟繁忙的系统。然后，我们将启动一个具有故意较低优先级（较高 `nice` 值）的进程来观察效果。
    
    首先，启动三个常规实例（如果需要，根据你的 CPU 核心数进行调整，但至少要与虚拟处理器一样多以创建争用）：
    
    ```bash
    for i in {1..3}; do sha1sum /dev/zero & done
    ```
    
    接下来，启动第四个实例，其 `nice` 级别为 10。与其他人相比，此进程将具有较低的优先级。
    
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
    
    _(注意：你的输出中的 PID 值将有所不同。)_
    
2. **使用 `ps` 和 `pgrep` 命令显示每个进程的 PID、CPU 使用率百分比、`nice` 值和可执行文件名**。  
    观察 `%CPU` 和 `NI` 列。`nice` 值为 10 的实例应显示比其他实例更低的 CPU 使用率百分比，因为调度程序为其分配的 CPU 时间较少。
    
    ```bash
    ps -o pid,pcpu,nice,comm $(pgrep sha1sum)
    ```
    
    查找 `NI` 值为 `10` 的进程。它的 `%CPU` 应该明显低于其他进程。
    
    ```plaintext
        PID %CPU  NI COMMAND
       5443 56.8   0 sha1sum
       5444 58.0   0 sha1sum
       5445 56.5   0 sha1sum
       5446  6.7  10 sha1sum
    ```
    
    _(注意：确切的 `%CPU` 值将根据系统负载和核心数而变化，但 `nice 10` 的进程应该具有较低的份额。)_
    
3. **使用 `sudo renice` 命令将其中一个常规进程的 `nice` 级别更改为 5**。  
    `renice` 命令允许你更改已运行进程的 `nice` 值。我们将通过将其中一个常规进程（nice 值为 0）更改为 nice 值为 5 来演示这一点。
    
    首先，从上一个 `ps` 命令的输出中识别 `sha1sum` 进程之一的 PID，该进程的 `nice` 值为 0。让我们使用上面示例中的第一个进程（PID 5443）。
    
    ```bash
    sudo renice -n 5 <PID_of_regular_process>
    ```
    
    将 `<PID_of_regular_process>` 替换为你标识的实际 PID。例如：
    
    ```bash
    sudo renice -n 5 5443
    ```
    
    你应该看到确认优先级更改的输出：
    
    ```plaintext
    5443 (process ID) old priority 0, new priority 5
    ```
    
4. **重复 `ps` 和 `pgrep` 命令以显示 CPU 百分比和 `nice` 级别**。  
    观察你修改其 `nice` 值的进程的 CPU 使用率变化。与 nice 值为 0 的进程相比，nice 值为 5 的进程现在应该具有略低的 CPU 使用率，但高于 nice 值为 10 的进程。
    
    ```bash
    ps -o pid,pcpu,nice,comm $(pgrep sha1sum)
    ```
    
    你应该看到已修改进程的 `NI` 值现在为 `5`，并且其 CPU 使用率反映了其新的优先级级别。
    
    ```plaintext
        PID %CPU  NI COMMAND
       5443 55.4   5 sha1sum
       5444 67.2   0 sha1sum
       5445 67.1   0 sha1sum
       5446  7.5  10 sha1sum
    ```
    
    _(注意：确切的 `%CPU` 值将有所不同，但你应该观察到具有较低 nice 值（较高优先级）的进程获得更多 CPU 时间。)_

## 清理正在运行的进程

在最后一步，你将确保在实验期间启动的所有后台进程都已正确终止。这是一个关键的清理步骤，以防止意外的资源消耗，并确保为将来的使用重置实验环境。

1. **使用 `pkill` 命令终止所有名称模式为 `sha1sum` 的正在运行的进程**。  
    `pkill` 命令是一种基于进程名称向进程发送信号（默认情况下为 `SIGTERM`）的有效方法。这将停止你在前面步骤中启动的所有 `sha1sum` 进程。
    
    ```bash
    pkill sha1sum
    ```
    
    你可能会看到指示进程已被终止的消息。
    
    ```plaintext
    [3]-  Terminated              sha1sum /dev/zero
    [2]-  Terminated              sha1sum /dev/zero
    [4]+  Terminated              nice -n 10 sha1sum /dev/zero
    [1]+  Terminated              sha1sum /dev/zero
    ```
    
2. **验证没有 `sha1sum` 进程仍在运行**。  
    你可以使用 `pgrep` 检查是否有任何 `sha1sum` 进程仍然处于活动状态。如果 `pgrep` 没有返回任何输出，则表示没有此类进程正在运行。
    
    ```bash
    pgrep sha1sum
    ```
    
    此命令应该不返回任何输出，表明所有 `sha1sum` 进程都已成功终止。
    
    ```plaintext
    $ pgrep sha1sum
    $
    ```

## 总结

在这个实验中，我们学习了如何在 RHEL 上管理和利用 `tuned` 进行系统性能优化。我们首先通过验证 `tuned` 服务的安装和状态以及列出可用的调优配置文件开始，了解到 `tuned` 使用这些配置文件动态地调整系统设置以适应特定的工作负载。然后，我们练习通过 SSH 以 `labex` 用户的身份登录到模拟的 `servera` 环境，并使用 `dnf list tuned` 确认了 `tuned` 软件包的安装。

该实验进一步指导我们更改 `tuned` 配置文件以观察它们对系统参数的影响，展示了不同的配置文件如何改变系统行为。我们还获得了启动和监控 CPU 密集型进程的实践经验，这对于识别性能瓶颈至关重要。最后，我们学习了使用 `nice` 和 `renice` 命令调整进程优先级以有效地管理资源分配，并通过清理正在运行的进程来将系统恢复到其初始状态。