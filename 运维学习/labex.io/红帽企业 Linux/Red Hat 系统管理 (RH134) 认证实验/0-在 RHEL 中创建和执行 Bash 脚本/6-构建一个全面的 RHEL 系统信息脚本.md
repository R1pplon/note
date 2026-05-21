# 构建一个全面的 RHEL 系统信息脚本

在最后一步中，你将结合到目前为止学到的所有概念——Bash 脚本、`for` 循环、`ssh` 用于远程执行，以及 `grep` 和正则表达式用于过滤——来构建一个全面的脚本，该脚本从多个 RHEL 服务器收集系统信息。该脚本将收集的数据保存到每个服务器的单独输出文件中。

1. **导航到你的脚本目录。**
    确保你位于 `~/project/scripts` 目录中。

    ```bash
    cd ~/project/scripts
    ```

2. **创建一个名为 `system_info.sh` 的新脚本。**
    此脚本将使用不同的方法收集系统信息以演示这些概念，并将输出重定向到你 `~/project` 目录中的不同文件。

    使用 `nano` 打开 `system_info.sh`：

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

    按 `Ctrl+O` 保存文件，然后按 `Enter`，最后按 `Ctrl+X` 退出 `nano`。

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

4. **执行 `system_info.sh` 脚本。**
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
    检查你 `~/project` 目录中 `output-basic.txt` 和 `output-detailed.txt` 文件的内容，以验证脚本是否按预期收集了信息。

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
