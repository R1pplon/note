# 创建处理命令输出的 Shell 脚本

在这一步骤中，你将创建一个 Shell 脚本，用于捕获 `ls -l` 命令的输出，提取特定信息（文件大小和文件名），并以格式化的方式显示出来。此练习演示了如何解析命令输出，这是 Shell 脚本编写中的常见任务。

## 任务

- 创建一个捕获 `ls -l` 命令输出的 Shell 脚本。
- 从每个文件的 `ls -l` 输出中提取文件大小和文件名。
- 以特定的格式显示提取的信息。

## 要求

- 在 `~/project/scripts` 目录下创建脚本。
- 脚本命名为 `process_ls.sh`。
- 脚本必须以 `#!/bin/bash` 解释器声明（shebang）开头。
- 脚本应捕获 `ls -l` 命令的输出并将其存储在变量中。
- 对于 `ls -l` 输出中列出的每个文件，提取其大小和名称。
- 按照以下格式显示提取的文件大小和文件名：

  ```
  File size: <file_size> bytes
  File name: <file_name>
  ```

  将 `<file_size>` 替换为实际的文件字节大小，将 `<file_name>` 替换为实际的文件名。

## 示例

假设你的目录中有一些文件。运行脚本时，输出应类似于：

```bash
[labex@host ~]$ cd ~/project/scripts
[labex@host scripts]$ nano process_ls.sh
[labex@host scripts]$ chmod +x process_ls.sh
[labex@host scripts]$ ./process_ls.sh
File size: 1300 bytes
File name: process_ls.sh
[labex@host scripts]$
```

具体的文件大小和名称将取决于你当前目录的内容。

## 提示

- 你可以使用命令替换（例如 `variable=$(command)`）来捕获命令的输出。
- 要处理命令输出的每一行，可以使用 `while read` 循环。
- `ls -l` 命令提供了详细信息。你可能需要使用 `awk` 或 `cut` 等工具从输出的每一行中提取特定的列（字段）。
- 请记住，`ls -l` 输出的第一行通常以「total」开头，在处理单个文件时应跳过该行。
- 确保你的脚本具有执行权限（`chmod +x`）。
