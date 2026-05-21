# 在 RHEL 服务器上创建并执行带有 For 循环的 Bash 脚本

在这一步中，你将把你在上一步中学习的 `for` 循环封装到一个 Bash 脚本中。这允许你保存自动化逻辑并轻松地重用它。你还将学习 `PATH` 环境变量以及如何从任何目录访问你的脚本。

1. **导航到你的脚本目录。**
    确保你位于 `~/project/scripts` 目录中。

    ```bash
    cd ~/project/scripts
    ```

2. **创建一个新的脚本用于主机名检索。**
    你将创建一个名为 `get_hostnames.sh` 的脚本，其中包含 `for` 循环，用于使用不同的选项检索主机名信息。

    使用 `nano` 打开 `get_hostnames.sh`：

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

    按 `Ctrl+O` 保存文件，然后按 `Enter`，最后按 `Ctrl+X` 退出 `nano`。

    让我们分解新元素：

    - `# This script...`: 以 `#` 开头的行是注释。它们被 shell 忽略，但对于记录你的脚本很有用。
    - `echo "Getting hostname with option: ${OPTION}"`: 此行在脚本执行期间提供反馈，指示当前正在使用哪个选项。
    - `exit 0`: 此命令显式地以状态码 `0` 退出脚本，这通常表示成功。

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

5. **理解 `PATH` 环境变量。**
    `PATH` 环境变量是 shell 搜索可执行命令的目录列表。当你键入 `ls` 或 `grep` 等命令时，shell 会在 `PATH` 中列出的目录中查找相应的可执行文件。

    显示你当前的 `PATH` 变量：

    ```bash
    echo $PATH
    ```

    你将看到一个以冒号分隔的目录列表。例如：

    ```plaintext
    /home/labex/.local/bin:/home/labex/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
    ```

    请注意，`~/project/scripts`（或 `/home/labex/project/scripts`）通常不包含在默认的 `PATH` 中。这就是你必须使用 `./get_hostnames.sh` 来执行你的脚本的原因。

6. **将你的脚本目录添加到 `PATH`（可选，供将来参考）。**
    虽然对于此实验步骤来说并非严格要求，但将个人 `bin` 或 `scripts` 目录添加到你的 `PATH` 中是很常见的做法，这样你就可以从任何位置运行你的自定义脚本。你可以通过将类似 `export PATH=$PATH:~/project/scripts` 的行添加到你的 `~/.bashrc` 或 `~/.zshrc` 文件中来实现。对于此实验，我们将继续通过指定脚本的路径来执行脚本。
