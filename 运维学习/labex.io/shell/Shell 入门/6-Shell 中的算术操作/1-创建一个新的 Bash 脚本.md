# 创建一个新的 Bash 脚本

让我们从创建一个新的 Bash 脚本文件开始。

1. 在 WebIDE 中打开你的终端。你应该会看到一个命令提示符，可能类似于这样：`labex@ubuntu:~/project$`。

2. 我们将在 `project` 目录中创建脚本。默认情况下，你已经在这个目录中，但为了确保，我们可以使用 `cd` 命令：

   ```bash
   cd ~/project
   ```

   这条命令将当前目录切换到 `/home/labex/project`。

3. 现在，让我们创建一个名为 `fruit_basket.sh` 的新文件。我们将使用 `touch` 命令，它会创建一个空文件：

   ```bash
   touch fruit_basket.sh
   ```

4. 在 WebIDE 编辑器中打开 `fruit_basket.sh` 文件。你可以通过点击 WebIDE 左侧文件资源管理器中的文件名来打开它。

5. 每个 Bash 脚本都应该以 "shebang" 行开头。这一行告诉系统使用哪个解释器来运行脚本。在文件的开头添加以下行：

   ```bash
   #!/bin/bash
   ```

   这一行指定脚本应使用 Bash 解释器运行。
