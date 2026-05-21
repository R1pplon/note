# 使用系统命令增强 Bash 脚本并使其可执行

在这一步中，你将通过添加更多系统命令来增强你的 Bash 脚本，并学习如何使脚本直接可执行。使脚本可执行允许你像运行任何其他命令一样运行它，而无需显式调用 `bash` 解释器。

1. **导航到你的脚本目录。**
    确保你位于 `~/project/scripts` 目录中，你在上一步中创建了 `firstscript.sh`。

    ```bash
    cd ~/project/scripts
    ```

2. **编辑 `firstscript.sh` 以包含更多系统命令。**
    你现在将向你的脚本添加命令，这些命令显示系统信息，例如块设备和文件系统可用空间。这演示了脚本如何自动化系统数据的收集。

    使用 `nano` 打开 `firstscript.sh`：

    ```bash
    nano firstscript.sh
    ```

    修改文件的内容以匹配以下内容。此脚本现在将：

    - 打印块设备信息的标题。
    - 执行 `lsblk` 以列出块设备。
    - 打印文件系统可用空间信息的标题。
    - 执行 `df -h` 以人类可读的格式显示磁盘空间使用情况。

    ```bash
    #!/usr/bin/bash
    echo "Hello, LabEx! This is my first Bash script."
    echo "#####################################################"
    echo "LIST BLOCK DEVICES"
    echo "#####################################################"
    lsblk
    echo "#####################################################"
    echo "FILESYSTEM FREE SPACE STATUS"
    echo "#####################################################"
    df -h
    ```

    按 `Ctrl+O` 保存文件，然后按 `Enter`，最后按 `Ctrl+X` 退出 `nano`。

3. **使脚本可执行。**
    要直接运行脚本（例如，`./firstscript.sh`），你需要授予它可执行权限。`chmod` 命令用于更改文件权限。`+x` 为所有用户添加执行权限。

    ```bash
    chmod +x firstscript.sh
    ```

    如果成功，此命令将不会有直接输出。

4. **直接执行脚本。**
    现在脚本是可执行的，你可以通过指定其路径来运行它。由于它在你的当前目录中，你使用 `./` 后跟脚本名称。

    ```bash
    ./firstscript.sh
    ```

    你应该看到类似于这样的输出，它结合了你的初始消息以及 `lsblk` 和 `df -h` 的输出：

    ```plaintext
    Hello, LabEx! This is my first Bash script.
    #####################################################
    LIST BLOCK DEVICES
    #####################################################
    NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    loop0         7:0    0 10.2G  1 loop /
    loop1         7:1    0  200M  1 loop /usr/local/bin
    loop2         7:2    0  200M  1 loop /usr/local/go
    loop3         7:3    0  200M  1 loop /usr/local/java
    loop4         7:4    0  200M  1 loop /usr/local/node
    loop5         7:5    0  200M  1 loop /usr/local/python
    #####################################################
    FILESYSTEM FREE SPACE STATUS
    #####################################################
    Filesystem      Size  Used Avail Use% Mounted on
    overlay          10G  4.0G  6.1G  40% /
    tmpfs            64M     0   64M   0% /dev
    tmpfs           7.8G     0  7.8G   0% /sys/fs/cgroup
    shm              64M     0   64M   0% /dev/shm
    /dev/loop0       10G  4.0G  6.1G  40% /
    tmpfs           7.8G     0  7.8G   0% /proc/asound
    tmpfs           7.8G     0  7.8G   0% /proc/acpi
    tmpfs           7.8G     0  7.8G   0% /proc/scsi
    tmpfs           7.8G     0  7.8G   0% /sys/firmware
    ```

    `lsblk` 和 `df -h` 的确切输出可能因特定环境而略有不同，但命令的结构和输出的存在应该类似。
