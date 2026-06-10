# 创建 Shell 变量

Shell 变量是通过使用 `=` 符号为其赋值来创建的。让我们从创建一个定义了一些变量的简单 Shell 脚本开始。

1. 在你的 WebIDE (VS Code) 中打开终端。

2. 在 `/home/labex/project` 目录下创建一个名为 `variables.sh` 的新文件：

   ```bash
   touch /home/labex/project/variables.sh
   ```

3. 在 WebIDE 中打开 `variables.sh` 文件并添加以下内容：

   ```bash
   #!/bin/bash
   
   PRICE_PER_APPLE=5
   MyFirstLetters=ABC
   greeting='Hello        world!'
   
   echo "Price per apple: $PRICE_PER_APPLE"
   echo "My first letters: $MyFirstLetters"
   echo "Greeting: $greeting"
   ```

   在这个脚本中，我们创建了三个变量：

   - `PRICE_PER_APPLE`：一个整数变量
   - `MyFirstLetters`：一个字符串变量
   - `greeting`：一个包含多个空格的字符串变量

4. 保存文件。

5. 为脚本添加可执行权限：

   ```bash
   chmod +x /home/labex/project/variables.sh
   ```

6. 运行脚本：

   ```bash
   ./variables.sh
   ```

   你应该会看到以下输出：

   ```
   Price per apple: 5
   My first letters: ABC
   Greeting: Hello        world!
   ```

   请注意，当使用单引号定义变量且在 echo 语句中不加引号时，`greeting` 变量中的额外空格在输出中得到了保留。
