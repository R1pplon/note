# 修改 Trap 以调用函数

除了使用简单的函数外，我们还可以修改脚本，通过 `trap` 命令调用更复杂的逻辑。这使我们能够在接收到信号时执行更详细的操作。

1. 在 WebIDE 编辑器中打开 `trap_example.sh` 文件。

2. 将文件内容替换为以下代码：

   ```bash
   #!/bin/bash
   
   cleanup_and_exit() {
     echo -e "\nSignal received! Cleaning up..."
     echo "Performing cleanup tasks..."
     # Add any necessary cleanup code here
     echo "Cleanup completed."
     echo "Exiting script gracefully."
     exit 0
   }
   
   trap cleanup_and_exit SIGINT SIGTERM
   
   echo "This script will run until you press Ctrl+C."
   echo "Press Ctrl+C to see the trap function in action and exit gracefully."
   
   count=1
   while true; do
     echo "Script is running... (iteration $count)"
     sleep 1
     ((count++))
   done
   ```

   让我们看看有哪些变化：

   - 我们扩展了 `cleanup_and_exit` 函数，包含了更详细的消息和清理任务的占位符。
   - 该函数现在模拟了一个更真实的清理过程，其中可能包括关闭文件句柄、删除临时文件或释放其他资源等任务。
   - 我们更新了主循环以显示迭代次数，从而更清晰地展示脚本正在活跃运行。

3. 完成修改后保存文件。

4. 再次运行脚本并通过按下 Ctrl+C 进行测试：

   ```bash
   ~/project/trap_example.sh
   ```

   当你中断脚本时，你应该会看到来自 `cleanup_and_exit` 函数的新消息，这演示了脚本是如何优雅退出的。
