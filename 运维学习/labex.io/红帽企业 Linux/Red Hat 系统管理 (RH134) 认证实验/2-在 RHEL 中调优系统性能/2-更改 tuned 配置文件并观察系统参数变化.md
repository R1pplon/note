# 更改 tuned 配置文件并观察系统参数变化

在这一步，你将学习如何更改活动的 `tuned` 配置文件，并观察对系统参数的即时影响。更改 `tuned` 配置文件允许你快速应用一组针对不同工作负载（例如，吞吐量密集型任务或节能）量身定制的性能优化。

1. **将当前活动调优配置文件更改为 `throughput-performance`**。
    `throughput-performance` 配置文件专为需要高吞吐量的系统而设计，通常会牺牲一些延迟。它通常针对磁盘 I/O 和网络性能进行优化。使用 `tuned-adm profile` 命令切换配置文件。

    ```bash
    sudo tuned-adm profile throughput-performance
    ```

    系统将提示你输入密码。

    ```plaintext
    $ sudo tuned-adm profile throughput-performance
    [sudo] password for user:
    ```

2. **确认新的活动配置文件**。
    更改配置文件后，最好验证新的配置文件确实处于活动状态。你可以使用 `tuned-adm active` 来执行此操作。

    ```bash
    sudo tuned-adm active
    ```

    输出现在应该显示 `throughput-performance` 作为活动配置文件。

    ```plaintext
    Current active profile: throughput-performance
    ```

3. **验证 `vm.dirty_ratio` 和 `vm.swappiness` 参数是否已更改**。
    `throughput-performance` 配置文件修改与内存管理相关的内核参数，例如 `vm.dirty_ratio` 和 `vm.swappiness`。即使 `virtual-guest` 配置文件继承自 `throughput-performance`，直接切换到 `throughput-performance` 配置文件也会应用基本值，而不会进行 virtual-guest 特定的修改。

    - `vm.dirty_ratio`：此参数定义系统内存中可以被脏页（已修改但尚未写入磁盘的页面）填充的最大百分比，然后系统开始将它们写入磁盘。较高的值可以通过允许在内存中缓冲更多数据来提高吞吐量。
    - `vm.swappiness`：此参数控制内核将匿名内存（应用程序数据）从 RAM 换出到交换空间的积极程度。较低的值意味着内核将尝试在 RAM 中保留更多应用程序数据，这通常对性能更好。

    让我们使用 `sysctl` 检查它们的当前值。

    ```bash
    sysctl vm.dirty_ratio
    sysctl vm.swappiness
    ```

    你应该观察到这些值已从 `virtual-guest` 配置文件设置（dirty_ratio = 30，vm.swappiness = 30）更改为基本 `throughput-performance` 配置文件值：

    ```plaintext
    vm.dirty_ratio = 40
    vm.swappiness = 10
    ```

    *(注意：这些值反映了基本 throughput-performance 优化，没有 virtual-guest 特定的修改。)*
