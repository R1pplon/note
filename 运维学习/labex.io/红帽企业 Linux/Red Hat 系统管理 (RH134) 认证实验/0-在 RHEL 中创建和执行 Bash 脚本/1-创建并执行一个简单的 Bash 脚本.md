# 创建并执行一个简单的 Bash 脚本

在这一步中，你将学习如何创建一个基本的 Bash 脚本并执行它。Bash 脚本是纯文本文件，其中包含 Bash shell 可以执行的一系列命令。它们是自动化重复性任务并将多个命令组合成一个可执行单元的强大工具。

首先，你将创建一个新目录来存储你的脚本。组织你的文件是一个好习惯，将脚本放在一个专门的目录中有助于保持你的主目录整洁。

1. **为你的脚本创建一个目录。**
    使用 `mkdir` 命令在你的 `~/project` 目录下创建一个名为 `scripts` 的目录。你将在此处存储你的 Bash 脚本。

    ```bash
    mkdir ~/project/scripts
    ```

    如果成功，此命令将不会有直接输出。

2. **创建你的第一个 Bash 脚本文件。**
    进入新创建的 `scripts` 目录，并使用 `nano` 文本编辑器创建一个名为 `firstscript.sh` 的文件。`.sh` 扩展名是 shell 脚本的常见约定，尽管并非严格要求。

    ```bash
    cd ~/project/scripts
    nano firstscript.sh
    ```

    在 `nano` 编辑器中，你将看到一个空白屏幕。

3. **向你的脚本添加内容。**
    每个 Bash 脚本都应该以 "shebang" 行 `#!/usr/bin/bash` 开头。这行告诉操作系统使用哪个解释器来执行脚本（在本例中是 Bash）。在 shebang 之后，你将添加一个简单的 `echo` 命令，用于将消息打印到终端。

    在 `nano` 编辑器中输入以下几行：

    ```bash
    #!/usr/bin/bash
    echo "Hello, LabEx! This is my first Bash script."
    ```

    输入内容后，按 `Ctrl+O` 保存文件，然后按 `Enter` 确认文件名，最后按 `Ctrl+X` 退出 `nano`。

    你的终端应该返回到命令提示符。

4. **使用 `bash` 解释器执行你的脚本。**
    你可以通过明确告诉 `bash` 解释器来运行 Bash 脚本。这种方法不需要脚本具有可执行权限。

    ```bash
    bash firstscript.sh
    ```

    你应该看到你的脚本的输出：

    ```plaintext
    Hello, LabEx! This is my first Bash script.
    ```

    这确认了你的脚本已正确创建并成功执行。
