# 配置和使用 Shell 变量与别名

在这一步中，你将学习如何配置和使用 shell 变量和别名。这些是强大的功能，允许你自定义你的 shell 环境，存储数据，并为常用命令创建快捷方式，从而显著提高你的命令行效率。

### Shell 变量

Shell 变量是存储数据的命名实体。它们可以存储数字、文本或其他数据，这些数据可以被 shell 或在 shell 中执行的程序使用。

1. 确保你在你的 `~/project` 目录中。

    ```bash
    cd ~/project
    ```

    ```plaintext
    [labex@host project]$
    ```

2. **设置局部变量**：让我们创建一个名为 `MY_MESSAGE` 的简单变量。

    ```bash
    MY_MESSAGE="Hello, LabEx!"
    ```

    请注意，`=` 符号周围没有空格。

3. **访问变量**：要访问变量的值，请在其名称前加上 `$` 符号。

    ```bash
    echo $MY_MESSAGE
    ```

    ```plaintext
    Hello, LabEx!
    ```

4. **使用大括号进行变量扩展**：有时，你需要清楚地分隔变量名，尤其是在它后面跟着其他字符时。为此使用花括号 `{}`。

    ```bash
    echo "The message is: ${MY_MESSAGE}."
    ```

    ```plaintext
    The message is: Hello, LabEx!.
    ```

    如果你省略大括号，shell 可能会将 `MY_MESSAGE.` 解释为变量名，而该变量名不存在。

5. **列出所有已设置的变量**：你可以使用 `set` 命令列出所有当前已设置的 shell 变量和函数。此输出可能很长，因此通常通过管道传递给 `less`。

    ```bash
    set | less
    ```

    ```plaintext
    BASH=/usr/bin/bash
    BASHOPTS=checkwinsize:cmdhist:complete_fullquote:expand_aliases:extglob:extquote:force_fignore:histappend:interactive_comments:progcomp:promptvars:sourcepath
    ... (press 'q' to quit less) ...
    ```

    按 `q` 退出 `less`。

6. **取消设置变量**：要删除变量，请使用 `unset` 命令。

    ```bash
    unset MY_MESSAGE
    ```

7. 验证该变量是否不再设置。

    ```bash
    echo $MY_MESSAGE
    ```

    ```plaintext

    ```

    你应该看到一个空行，这表示该变量已取消设置。

### 环境变量

环境变量是一种特殊类型的 shell 变量，由子进程继承。这意味着从你当前 shell 启动的任何程序或脚本都将有权访问这些变量。它们通常用于配置应用程序的环境。

1. **设置环境变量**：使用 `export` 命令将变量设置为环境变量。

    ```bash
    export EDITOR=vim
    ```

    这将设置 `EDITOR` 环境变量，许多程序使用它来确定你喜欢的文本编辑器。

2. **列出环境变量**：使用 `env` 命令仅列出环境变量。

    ```bash
    env | grep EDITOR
    ```

    ```plaintext
    EDITOR=vim
    ```

3. **取消导出变量**：你可以使用 `export -n` 取消导出变量，而无需取消设置它。这使其再次成为局部变量。

    ```bash
    export -n EDITOR
    ```

4. 验证它不再是环境变量。

    ```bash
    env | grep EDITOR
    ```

    ```plaintext

    ```

    你应该看不到任何输出。但是，它仍然是一个局部变量：

    ```bash
    echo $EDITOR
    ```

    ```plaintext
    vim
    ```

5. 要完全删除它，请使用 `unset`。

    ```bash
    unset EDITOR
    ```

### Shell 别名

别名是命令的快捷方式。它们允许你定义一个新命令，该命令扩展为更长的命令或一系列命令。这对于经常使用且带有许多选项的命令非常有用。

1. **创建别名**：让我们为 `ls -l` 创建一个别名，使其更短。

    ```bash
    alias ll='ls -l'
    ```

    请注意命令周围的单引号，以确保将其视为单个字符串。

2. **使用别名**：现在，你可以简单地键入 `ll` 而不是 `ls -l`。

    ```bash
    ll
    ```

    ```plaintext
    total 24
    -rw-r--r-- 1 labex labex 123 Jan XX HH:MM combined_output.log
    ... (output of ls -l) ...
    ```

3. **列出别名**：使用不带任何参数的 `alias` 命令查看所有已定义的别名。

    ```bash
    alias
    ```

    ```plaintext
    alias ll='ls -l'
    ```

    你可能会看到其他默认别名，具体取决于你的 shell 配置。

4. **创建更复杂的别名**：你还可以为带有参数或多个命令的命令创建别名。

    ```bash
    alias myip='ip a | grep "inet " | grep -v "127.0.0.1" | awk "{print \$2}" | cut -d/ -f1'
    ```

    在这里，`myip` 将显示你的主 IP 地址。请注意 `\$2` 以转义 `$` 符号，以便将其传递给 `awk`，而不是在定义别名时由 shell 解释。

5. 测试 `myip` 别名。

    ```bash
    myip
    ```

    ```plaintext
    172.17.0.2
    ```

    （你的 IP 地址可能有所不同）

6. **取消设置别名**：要删除别名，请使用 `unalias` 命令。

    ```bash
    unalias ll
    ```

7. 验证别名是否已删除。

    ```bash
    alias
    ```

    ```plaintext
    alias myip='ip a | grep "inet " | grep -v "127.0.0.1" | awk "{print \$2}" | cut -d/ -f1'
    ```

    `ll` 应该不再在列表中。

Shell 变量和别名是临时的，当你关闭终端会话时，它们将丢失。要使它们永久生效，你需要将它们添加到你的 shell 的配置文件（例如，`~/.bashrc` 或 `~/.profile`）中，这将在更高级的主题中介绍。
