# 实现基础 Trap 命令

现在，我们在脚本中实现一个基础的 `trap` 命令，以捕获特定信号并优雅地退出。

1. 将以下内容添加到 `trap_example.sh` 文件中：

   ```bash
   #!/bin/bash
   
   cleanup_and_exit() {
     echo -e "\nSignal received! Cleaning up and exiting..."
     exit 0
   }
   
   trap cleanup_and_exit SIGINT SIGTERM
   
   echo "This script will run until you press Ctrl+C."
   echo "Press Ctrl+C to see the trap in action and exit gracefully."
   
   count=1
   while true; do
     echo "Script is running... (iteration $count)"
     sleep 1
     ((count++))
   done
   ```

   让我们来分析一下这个脚本：

   - 第一行 `#!/bin/bash` 被称为 Shebang。它告诉系统该脚本应由 Bash Shell 执行。
   - 我们定义了一个 `cleanup_and_exit` 函数，用于打印一条消息并退出脚本。
   - `trap` 命令被设置为在捕获到 SIGINT（中断）或 SIGTERM（终止）信号时调用 `cleanup_and_exit`。当你按下 Ctrl+C 时，通常会发送 SIGINT 信号；而当系统请求进程优雅终止时，通常会使用 SIGTERM。
   - `echo` 命令为用户打印操作指令。
   - `while` 循环会无限运行，每秒打印一次消息并递增计数器。

2. 添加完内容后保存文件。
