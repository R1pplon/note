# 创建带有输入参数的 Shell 脚本

在这一步中，你将创建一个从命令行接收两个输入参数的 Shell 脚本。随后，这些参数将在脚本内部用于执行简单的算术运算。理解如何处理输入参数对于编写灵活且可重用的脚本至关重要。

## 任务

- 在 `~/project` 目录下创建一个名为 `script.sh` 的 Shell 脚本。
- 该脚本应接收两个数字作为输入参数。
- 该脚本应计算这两个参数的和。
- 该脚本应将加法运算的结果打印到标准输出。

## 要求

- Shell 脚本必须命名为 `script.sh`。
- 脚本必须位于 `~/project` 目录中。
- 脚本必须使用 `$1` 和 `$2` 变量分别访问第一个和第二个输入参数。
- 脚本必须对两个输入参数执行加法运算。
- 脚本的输出格式必须严格为 `The result is: <sum>`，其中 `<sum>` 是计算出的总和。

## 示例

以下是脚本执行时的行为示例：

```bash
[labex@host ~]$ mkdir -p ~/project
[labex@host ~]$ cd ~/project
[labex@host project]$ nano script.sh
# (创建并保存脚本后)
[labex@host project]$ chmod +x script.sh
[labex@host project]$ ./script.sh 10 20
The result is: 30
[labex@host project]$ ./script.sh 5 8
The result is: 13
```

## 提示

- 你可以使用 `nano` 来创建和编辑脚本文件。
- 记得在运行脚本之前，使用 `chmod` 命令赋予脚本可执行权限。
- 在 Bash 中，可以使用 `((...))` 或 `$[...]` 语法执行算术运算。例如：`result=$((num1 + num2))`。
- 可以使用 `echo` 命令将输出打印到终端。
