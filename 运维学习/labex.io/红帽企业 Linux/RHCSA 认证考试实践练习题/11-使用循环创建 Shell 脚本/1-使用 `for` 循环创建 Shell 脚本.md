# 使用 `for` 循环创建 Shell 脚本

在这一步骤中，你将创建一个 Shell 脚本，使用 `for` 循环遍历目录中的文件列表，并对每个文件执行操作。该脚本将演示如何动态处理多个文件，这是 Shell 脚本编写中的常见任务。

## 任务

- 创建一个 Shell 脚本，使用 `for` 循环遍历指定目录中的文件列表。
- 对于找到的每个文件，脚本应打印文件名及其内容。

## 要求

- 脚本必须命名为 `list_files.sh`。
- 脚本必须位于 `~/project` 目录下。
- 脚本必须以 `#!/bin/bash` 解释器声明（shebang）开头。
- 脚本必须接受一个目录路径作为其第一个命令行参数。
- 脚本必须使用 `for` 循环遍历所提供目录中的文件。
- 对于遇到的每个普通文件，脚本必须打印「File: 」后跟文件的基本名称（base name），然后打印该文件的全部内容。
- 如果未提供目录参数，脚本应打印用法提示信息并以非零状态码退出。

## 示例

假设你已经在 `~/project` 中创建了 `list_files.sh` 脚本并赋予了执行权限。当你以 `~/project` 作为参数运行它时，输出应类似于：

```bash
[labex@host ~]$ cd ~/project
[labex@host project]$ chmod +x list_files.sh
[labex@host project]$ ./list_files.sh ~/project
File: file1.txt
Content of file1.txt

File: file2.txt
Content of file2.txt

File: file3.txt
Content of file3.txt

[labex@host project]$
```

如果你在不带任何参数的情况下运行脚本，它应该显示用法信息：

```bash
[labex@host project]$ ./list_files.sh
Usage: ./list_files.sh <directory_path>
[labex@host project]$ echo $?
1
[labex@host project]$
```

文件的具体内容将取决于你在其中放置的内容。

## 提示

- 你可以使用特殊变量 `$1` 来访问传递给脚本的第一个命令行参数。
- 要遍历目录中的文件，可以使用通配符模式，例如 `"$1"/*`。
- 使用带有 `-f` 测试操作符的 `if` 语句（例如 `if [ -f "$file" ]`）来检查某个项目是否为普通文件而非目录。
- `basename` 命令对于从完整路径中提取纯文件名非常有用。例如，`basename /home/labex/project/file1.txt` 将输出 `file1.txt`。
- `cat` 命令可用于显示文件内容。
- 记得使用 `chmod +x` 使你的脚本具有可执行权限。
- 要检查变量是否为空，可以使用 `if [ -z "$variable" ]`。
- 要以特定的状态码退出脚本，请使用 `exit <status_code>`。
