# 编写条件判断 Shell 脚本

在本挑战中，你将学习如何创建一个简单的 Shell 脚本，利用条件语句根据特定条件执行相应的代码。

## 任务

- 创建一个 Shell 脚本，用于检查提供的命令行参数数量。
- 如果调用脚本时没有提供参数，则打印一条消息，提示该脚本至少需要一个参数。
- 如果调用脚本时提供了一个参数，则打印一条消息，说明脚本是在有一个参数的情况下调用的。
- 如果调用脚本时提供了多个参数，则打印一条消息，说明脚本是在有多个参数的情况下调用的。

## 要求

- Shell 脚本应命名为 `conditional_script.sh`，并存放在 `~/project` 目录下。
- 你可以使用 `nano` 来编辑脚本。
- 脚本应使用 `if-elif-else` 结构来处理不同的情况。
- 脚本应使用 `test` 命令或 `[]` 语法来检查参数数量。
- 脚本应使用 `echo` 或 `printf` 来打印相应的消息。

## 示例

```bash
sudo chmod +x ~/project/conditional_script.sh
```

```bash
$ ~/project/conditional_script.sh
This script requires at least one argument.
$ ~/project/conditional_script.sh one
This script was called with one argument.
$ ~/project/conditional_script.sh one two three
This script was called with multiple arguments.
```
