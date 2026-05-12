## 介绍

在这个实验（Lab）中，你将学习如何创建和执行用于 RHEL 系统管理的 Bash 脚本。你将从编写简单的脚本开始，然后使用系统命令增强它们，并使它们可执行。实验（Lab）将逐步进行，使用 `for` 循环来自动化任务，例如系统信息收集，并使用 `grep` 和正则表达式过滤命令输出。最后，你将构建一个全面的 RHEL 系统信息脚本，从而获得自动化管理任务和利用强大 shell 功能的实践技能。

## 创建并执行一个简单的 Bash 脚本

在这一步中，你将学习如何创建一个基本的 Bash 脚本并执行它。Bash 脚本是纯文本文件，其中包含 Bash shell 可以执行的一系列命令。它们是自动化重复性任务并将多个命令组合成一个可执行单元的强大工具。

首先，你将创建一个新目录来存储你的脚本。组织你的文件是一个好习惯，将脚本放在一个专门的目录中有助于保持你的主目录整洁。

1. **为你的脚本创建一个目录。**  
    使用 `mkdir` 命令在你的 `~/project` 目录下创建一个名为 `scripts` 的目录。你将在此处存储你的 Bash 脚本。
    
    ```bash
    mkdir ~/project/scripts
    ```
    
    如果成功，此命令将不会有直接输出。
    
2. **创建你的第一个 Bash 脚本文件。**  
    进入新创建的 `scripts` 目录，并使用 `nano` 文本编辑器创建一个名为 `firstscript.sh` 的文件。`.sh` 扩展名是 shell 脚本的常见约定，尽管并非严格要求。
    
    ```bash
    cd ~/project/scripts
    nano firstscript.sh
    ```
    
    在 `nano` 编辑器中，你将看到一个空白屏幕。
    
3. **向你的脚本添加内容。**  
    每个 Bash 脚本都应该以 "shebang" 行 `#!/usr/bin/bash` 开头。这行告诉操作系统使用哪个解释器来执行脚本（在本例中是 Bash）。在 shebang 之后，你将添加一个简单的 `echo` 命令，用于将消息打印到终端。
    
    在 `nano` 编辑器中输入以下几行：
    
    ```bash
    #!/usr/bin/bash
    echo "Hello, LabEx! This is my first Bash script."
    ```
    
    输入内容后，按 `Ctrl+O` 保存文件，然后按 `Enter` 确认文件名，最后按 `Ctrl+X` 退出 `nano`。
    
    你的终端应该返回到命令提示符。
    
4. **使用 `bash` 解释器执行你的脚本。**  
    你可以通过明确告诉 `bash` 解释器来运行 Bash 脚本。这种方法不需要脚本具有可执行权限。
    
    ```bash
    bash firstscript.sh
    ```
    
    你应该看到你的脚本的输出：
    
    ```plaintext
    Hello, LabEx! This is my first Bash script.
    ```
    
    这确认了你的脚本已正确创建并成功执行。

## 使用系统命令增强 Bash 脚本并使其可执行

在这一步中，你将通过添加更多系统命令来增强你的 Bash 脚本，并学习如何使脚本直接可执行。使脚本可执行允许你像运行任何其他命令一样运行它，而无需显式调用 `bash` 解释器。

1. **导航到你的脚本目录。**  
    确保你位于 `~/project/scripts` 目录中，你在上一步中创建了 `firstscript.sh`。
    
    ```bash
    cd ~/project/scripts
    ```
    
2. **编辑 `firstscript.sh` 以包含更多系统命令。**  
    你现在将向你的脚本添加命令，这些命令显示系统信息，例如块设备和文件系统可用空间。这演示了脚本如何自动化系统数据的收集。
    
    使用 `nano` 打开 `firstscript.sh`：
    
    ```bash
    nano firstscript.sh
    ```
    
    修改文件的内容以匹配以下内容。此脚本现在将：
    
    - 打印块设备信息的标题。
    - 执行 `lsblk` 以列出块设备。
    - 打印文件系统可用空间信息的标题。
    - 执行 `df -h` 以人类可读的格式显示磁盘空间使用情况。
    
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
    
    按 `Ctrl+O` 保存文件，然后按 `Enter`，最后按 `Ctrl+X` 退出 `nano`。
    
3. **使脚本可执行。**  
    要直接运行脚本（例如，`./firstscript.sh`），你需要授予它可执行权限。`chmod` 命令用于更改文件权限。`+x` 为所有用户添加执行权限。
    
    ```bash
    chmod +x firstscript.sh
    ```
    
    如果成功，此命令将不会有直接输出。
    
4. **直接执行脚本。**  
    现在脚本是可执行的，你可以通过指定其路径来运行它。由于它在你的当前目录中，你使用 `./` 后跟脚本名称。
    
    ```bash
    ./firstscript.sh
    ```
    
    你应该看到类似于这样的输出，它结合了你的初始消息以及 `lsblk` 和 `df -h` 的输出：
    
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
    
    `lsblk` 和 `df -h` 的确切输出可能因特定环境而略有不同，但命令的结构和输出的存在应该类似。

## 在 RHEL 服务器上使用 For 循环自动化主机名检索

在这一步中，你将学习 Bash 中的 `for` 循环，以及如何使用它们在多个服务器上自动化任务。`for` 循环是基本的控制流语句，它允许你为列表中的每个项目重复执行一个代码块。这对于有效地管理多个系统特别有用。

对于此练习，我们将使用 localhost 演示该概念，并使用不同的方法来模拟与多个服务器一起工作。

1. **使用本地命令检索主机名。**  
    在使用循环之前，让我们看看你通常如何从本地系统获取主机名。我们将使用不同的方法来演示这个概念。
    
    ```bash
    hostname
    hostname -f
    ```
    
    你应该看到类似于以下的输出：
    
    ```plaintext
    684791f71c0e35fea6cc1243
    684791f71c0e35fea6cc1243
    ```
    
    这表明你可以使用不同的选项成功检索主机名信息。请注意，在此容器环境中，所有主机名选项都返回相同的容器 ID。
    
2. **创建一个 `for` 循环，使用不同的选项自动化主机名检索。**  
    现在，让我们使用一个 `for` 循环来更有效地执行不同的主机名命令。`for` 循环将遍历主机名选项的列表，使用每个选项执行 `hostname` 命令。
    
    ```bash
    for OPTION in "" "-f" "-s"; do
      echo "hostname ${OPTION}:"
      hostname ${OPTION}
      echo ""
    done
    ```
    
    让我们分解这个命令：
    
    - `for OPTION in "" "-f" "-s";`: 这部分初始化循环。`OPTION` 是一个变量，它将在每次迭代期间获取列表中每个项目（空字符串、`-f`、`-s`）的值。
    - `do`: 标记要在循环中执行的命令的开始。
    - `echo "hostname ${OPTION}:";`: 这显示了正在使用的选项。
    - `hostname ${OPTION};`: 这是在每次迭代中执行的命令。`${OPTION}` 扩展为 `OPTION` 变量的当前值。
    - `echo "";`: 添加一个空行以提高可读性。
    - `done`: 标记循环的结束。
    
    你应该看到类似于以下的输出：
    
    ```plaintext
    hostname :
    684791f71c0e35fea6cc1243
    
    hostname -f:
    684791f71c0e35fea6cc1243
    
    hostname -s:
    684791f71c0e35fea6cc1243
    ```
    
    这演示了 `for` 循环在自动化具有不同参数的重复性任务方面的强大功能。

## 在 RHEL 服务器上创建并执行带有 For 循环的 Bash 脚本

在这一步中，你将把你在上一步中学习的 `for` 循环封装到一个 Bash 脚本中。这允许你保存自动化逻辑并轻松地重用它。你还将学习 `PATH` 环境变量以及如何从任何目录访问你的脚本。

1. **导航到你的脚本目录。**  
    确保你位于 `~/project/scripts` 目录中。
    
    ```bash
    cd ~/project/scripts
    ```
    
2. **创建一个新的脚本用于主机名检索。**  
    你将创建一个名为 `get_hostnames.sh` 的脚本，其中包含 `for` 循环，用于使用不同的选项检索主机名信息。
    
    使用 `nano` 打开 `get_hostnames.sh`：
    
    ```bash
    nano get_hostnames.sh
    ```
    
    将以下内容添加到文件中：
    
    ```bash
    #!/usr/bin/bash
    # This script retrieves hostname information using different options.
    
    for OPTION in "" "-f" "-s"; do
      echo "Getting hostname with option: ${OPTION}"
      hostname ${OPTION}
      echo "------------------------"
    done
    
    exit 0
    ```
    
    按 `Ctrl+O` 保存文件，然后按 `Enter`，最后按 `Ctrl+X` 退出 `nano`。
    
    让我们分解新元素：
    
    - `# This script...`: 以 `#` 开头的行是注释。它们被 shell 忽略，但对于记录你的脚本很有用。
    - `echo "Getting hostname with option: ${OPTION}"`: 此行在脚本执行期间提供反馈，指示当前正在使用哪个选项。
    - `exit 0`: 此命令显式地以状态码 `0` 退出脚本，这通常表示成功。
3. **使脚本可执行。**  
    就像在上一步中一样，你需要为你的新脚本提供可执行权限。
    
    ```bash
    chmod +x get_hostnames.sh
    ```
    
    如果成功，此命令将不会有直接输出。
    
4. **从其当前目录执行脚本。**  
    运行脚本以验证其功能。
    
    ```bash
    ./get_hostnames.sh
    ```
    
    你应该看到类似于以下的输出：
    
    ```plaintext
    Getting hostname with option:
    684791f71c0e35fea6cc1243
    ------------------------
    Getting hostname with option: -f
    684791f71c0e35fea6cc1243
    ------------------------
    Getting hostname with option: -s
    684791f71c0e35fea6cc1243
    ------------------------
    ```
    
5. **理解 `PATH` 环境变量。**  
    `PATH` 环境变量是 shell 搜索可执行命令的目录列表。当你键入 `ls` 或 `grep` 等命令时，shell 会在 `PATH` 中列出的目录中查找相应的可执行文件。
    
    显示你当前的 `PATH` 变量：
    
    ```bash
    echo $PATH
    ```
    
    你将看到一个以冒号分隔的目录列表。例如：
    
    ```plaintext
    /home/labex/.local/bin:/home/labex/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
    ```
    
    请注意，`~/project/scripts`（或 `/home/labex/project/scripts`）通常不包含在默认的 `PATH` 中。这就是你必须使用 `./get_hostnames.sh` 来执行你的脚本的原因。
    
6. **将你的脚本目录添加到 `PATH`（可选，供将来参考）。**  
    虽然对于此实验步骤来说并非严格要求，但将个人 `bin` 或 `scripts` 目录添加到你的 `PATH` 中是很常见的做法，这样你就可以从任何位置运行你的自定义脚本。你可以通过将类似 `export PATH=$PATH:~/project/scripts` 的行添加到你的 `~/.bashrc` 或 `~/.zshrc` 文件中来实现。对于此实验，我们将继续通过指定脚本的路径来执行脚本。

## 在 RHEL 上使用 Grep 和正则表达式过滤命令输出

在这一步中，你将学习如何使用 `grep` 命令与正则表达式，以有效地从命令输出和文件中过滤和提取特定信息。`grep` 是一个强大的实用程序，用于在纯文本数据集中搜索与正则表达式匹配的行。正则表达式（regex）是定义搜索模式的字符序列。

1. **在系统文件中搜索特定的用户和组信息。**  
    你将使用 `grep` 从 `/etc/passwd` 和 `/etc/group` 文件中查找关于 `labex` 用户和组的信息。这些文件分别存储用户和组帐户信息。
    
    首先，让我们在 `/etc/passwd` 中查找 `labex` 用户条目：
    
    ```bash
    grep "labex" /etc/passwd
    ```
    
    预期输出：
    
    ```plaintext
    labex:x:1000:1000::/home/labex:/bin/bash
    ```
    
    接下来，在 `/etc/group` 中查找 `labex` 组条目：
    
    ```bash
    grep "labex" /etc/group
    ```
    
    预期输出：
    
    ```plaintext
    labex:x:1000:
    ```
    
    这些命令演示了基本的 `grep` 用法，以查找完全字符串匹配。
    
2. **使用 `grep` 和正则表达式过滤 `lscpu` 输出。**  
    `lscpu` 命令显示 CPU 架构信息。通常，你只需要其广泛输出中的特定行。你可以使用 `grep` 和正则表达式来过滤以 "CPU" 开头的行。
    
    ```bash
    lscpu | grep '^CPU'
    ```
    
    让我们分解这个命令：
    
    - `lscpu`: 生成 CPU 信息。
    - `|`: 这是一个管道，它获取 `lscpu` 的标准输出，并将其作为标准输入提供给 `grep` 命令。
    - `grep '^CPU'`: 搜索以字面字符串 "CPU" 开头的行。`^`（插入符号）是一个正则表达式锚点，它匹配行的开头。
    
    预期输出（可能因环境而略有不同）：
    
    ```plaintext
    CPU op-mode(s):                     32-bit, 64-bit
    CPU(s):                             4
    CPU family:                         6
    ```
    
3. **过滤配置文件，忽略注释和空行。**  
    配置文件通常包含注释（以 `#` 开头的行）和空行，这些与实际配置无关。你可以使用 `grep` 和多个模式来排除这些行。让我们用 `/etc/passwd` 文件来演示这一点。
    
    ```bash
    grep -v '^#' /etc/passwd | head -5
    ```
    
    让我们分解这个命令：
    
    - `grep -v '^#' /etc/passwd`: `-v` 选项反转匹配，这意味着它选择_不_匹配该模式的行。`^#` 匹配以 `#` 开头的行。因此，这部分过滤掉注释行。
    - `|`: 将第一个 `grep` 命令的输出通过管道传递给下一个命令。
    - `head -5`: 仅显示输出的前 5 行。
    
    预期输出（显示没有注释的用户帐户条目）：
    
    ```plaintext
    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/bin:/sbin/nologin
    daemon:x:2:2:daemon:/sbin:/sbin/nologin
    adm:x:3:4:adm:/var/adm:/sbin/nologin
    lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
    ```
    
4. **在系统文件中搜索特定模式。**  
    你可以使用 `grep` 在各种系统文件中搜索特定模式。让我们在 `/etc/passwd` 文件中搜索与 shell 相关的条目。
    
    ```bash
    grep "bash" /etc/passwd
    ```
    
    预期输出（显示使用 bash shell 的用户）：
    
    ```plaintext
    root:x:0:0:root:/root:/bin/bash
    labex:x:1000:1000::/home/labex:/bin/bash
    ```
    
    此命令帮助你识别将 bash 作为默认 shell 的用户。

## 构建一个全面的 RHEL 系统信息脚本

在最后一步中，你将结合到目前为止学到的所有概念——Bash 脚本、`for` 循环、`ssh` 用于远程执行，以及 `grep` 和正则表达式用于过滤——来构建一个全面的脚本，该脚本从多个 RHEL 服务器收集系统信息。该脚本将收集的数据保存到每个服务器的单独输出文件中。

1. **导航到你的脚本目录。**  
    确保你位于 `~/project/scripts` 目录中。
    
    ```bash
    cd ~/project/scripts
    ```
    
2. **创建一个名为 `system_info.sh` 的新脚本。**  
    此脚本将使用不同的方法收集系统信息以演示这些概念，并将输出重定向到你 `~/project` 目录中的不同文件。
    
    使用 `nano` 打开 `system_info.sh`：
    
    ```bash
    nano system_info.sh
    ```
    
    将以下内容添加到文件中：
    
    ```bash
    #!/usr/bin/bash
    
    # Define variables for output directory
    OUT_DIR='/home/labex/project'
    
    # Loop through different information gathering approaches
    for APPROACH in "basic" "detailed"; do
      OUTPUT_FILE="${OUT_DIR}/output-${APPROACH}.txt"
    
      echo "Gathering ${APPROACH} system information..."
      # Clear previous output file or create a new one
      > "${OUTPUT_FILE}"
    
      # Get hostname information
      echo "### Hostname Information ###" >> "${OUTPUT_FILE}"
      if [ "${APPROACH}" = "basic" ]; then
        hostname >> "${OUTPUT_FILE}"
      else
        hostname -f >> "${OUTPUT_FILE}"
      fi
      echo "" >> "${OUTPUT_FILE}" # Add a blank line for readability
    
      # Get CPU information (only lines starting with CPU)
      echo "### CPU Information ###" >> "${OUTPUT_FILE}"
      lscpu | grep '^CPU' >> "${OUTPUT_FILE}"
      echo "" >> "${OUTPUT_FILE}"
    
      # Get system users with bash shell
      echo "### Users with Bash Shell ###" >> "${OUTPUT_FILE}"
      grep "bash" /etc/passwd >> "${OUTPUT_FILE}"
      echo "" >> "${OUTPUT_FILE}"
    
      # Get system information based on approach
      if [ "${APPROACH}" = "basic" ]; then
        echo "### Basic System Info ###" >> "${OUTPUT_FILE}"
        uname -r >> "${OUTPUT_FILE}"
      else
        echo "### Detailed System Info ###" >> "${OUTPUT_FILE}"
        uname -a >> "${OUTPUT_FILE}"
      fi
      echo "" >> "${OUTPUT_FILE}"
    
      echo "Information saved to ${OUTPUT_FILE}"
      echo "-----------------------------------------------------"
    done
    
    echo "Script execution complete."
    ```
    
    按 `Ctrl+O` 保存文件，然后按 `Enter`，最后按 `Ctrl+X` 退出 `nano`。
    
    此脚本的关键元素：
    
    - `OUT_DIR='/home/labex/project'`: 变量用于使脚本更灵活和更具可读性。
    - `OUTPUT_FILE="${OUT_DIR}/output-${APPROACH}.txt"`: 为每种方法动态构造输出文件名。
    - `> "${OUTPUT_FILE}"`: 这会将空命令的输出重定向到文件，如果文件存在，则有效地清除其内容，如果文件不存在，则创建它。这确保了每次运行都有一个新文件。
    - `>> "${OUTPUT_FILE}"`: 这会将命令的输出追加到指定的文件中。
    - `if [ "${APPROACH}" = "basic" ]; then ... else ... fi`: 条件语句，根据所使用的方法执行不同的命令。
    - `echo "### Section Header ###"`: 为输出文件添加清晰的标题，以便更好地组织。
3. **使脚本可执行。**
    
    ```bash
    chmod +x system_info.sh
    ```
    
    如果成功，此命令将不会有直接输出。
    
4. **执行 `system_info.sh` 脚本。**  
    运行你的综合脚本。它将使用不同的方法收集系统信息，并将结果保存到单独的文件中。
    
    ```bash
    ./system_info.sh
    ```
    
    你应该在你的终端中看到输出，指示脚本的进度：
    
    ```plaintext
    Gathering basic system information...
    Information saved to /home/labex/project/output-basic.txt
    -----------------------------------------------------
    Gathering detailed system information...
    Information saved to /home/labex/project/output-detailed.txt
    -----------------------------------------------------
    Script execution complete.
    ```
    
5. **查看生成的输出文件。**  
    检查你 `~/project` 目录中 `output-basic.txt` 和 `output-detailed.txt` 文件的内容，以验证脚本是否按预期收集了信息。
    
    ```bash
    cat ~/project/output-basic.txt
    cat ~/project/output-detailed.txt
    ```
    
    每个文件的内容应类似于这样（实际值会有所不同）：
    
    **output-basic.txt：**
    
    ```plaintext
    ### Hostname Information ###
    684791f71c0e35fea6cc1243
    
    ### CPU Information ###
    CPU op-mode(s):                     32-bit, 64-bit
    CPU(s):                             4
    CPU family:                         6
    
    ### Users with Bash Shell ###
    root:x:0:0:root:/root:/bin/bash
    labex:x:1000:1000::/home/labex:/bin/bash
    
    ### Basic System Info ###
    5.4.0-162-generic
    ```
    
    **output-detailed.txt：**
    
    ```plaintext
    ### Hostname Information ###
    684791f71c0e35fea6cc1243
    
    ### CPU Information ###
    CPU op-mode(s):                     32-bit, 64-bit
    CPU(s):                             4
    CPU family:                         6
    
    ### Users with Bash Shell ###
    root:x:0:0:root:/root:/bin/bash
    labex:x:1000:1000::/home/labex:/bin/bash
    
    ### Detailed System Info ###
    Linux 684791f71c0e35fea6cc1243 5.4.0-162-generic #179-Ubuntu SMP Mon Aug 14 08:51:31 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
    ```
    
    这个最终脚本演示了如何结合各种 Bash 功能和 Linux 命令来创建用于系统管理任务的强大自动化工具。

## 总结

在这个实验中，你学习了为 RHEL 系统管理创建和执行 Bash 脚本的基本步骤。你首先设置了一个专门用于脚本的目录，然后编写了一个简单的 Bash 脚本，理解了 shebang 行的重要性并使用了 `echo` 命令。你探索了不同的脚本执行方法，包括直接使用 `bash` 解释器以及通过使它们可执行。

此外，你通过结合系统命令和使用 `for` 循环自动化任务来增强你的脚本编写技能，从而使用不同的方法收集系统信息。你还学习了如何使用 `grep` 和正则表达式有效地过滤命令输出，这是解析系统信息的关键技能。最后，你应用了这些概念来构建一个全面的 RHEL 系统信息脚本，演示了如何结合各种命令和脚本结构来收集和呈现有价值的系统数据。