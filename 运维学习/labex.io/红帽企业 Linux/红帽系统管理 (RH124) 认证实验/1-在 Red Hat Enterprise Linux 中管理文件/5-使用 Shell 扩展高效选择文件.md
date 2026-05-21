# 使用 Shell 扩展高效选择文件

在这一步中，你将学习如何使用各种 Shell 扩展特性高效地选择文件和目录，包括模式匹配（Globbing）、波浪号扩展（Tilde Expansion）、花括号扩展（Brace Expansion）、变量扩展（Variable Expansion）和命令替换（Command Substitution）。这些特性允许你使用简洁的命令对多个文件执行操作。

首先，确保你位于 `~/project` 目录下。

```bash
cd ~/project
```

验证你当前的位置：

```bash
pwd
```

输出应为 `/home/labex/project`。

为了演示 Shell 扩展，让我们创建一组文件和目录。

```bash
mkdir data
cd data
touch file1.txt file2.log file_a.txt file_b.log report_2023.txt report_2024.log
touch image.jpg document.pdf archive.zip
mkdir dir1 dir2 dir3
cd ..
```

现在，列出 `data` 目录的内容，查看你创建的文件：

```bash
ls data
```

你应该看到：

```plaintext
archive.zip  dir1  dir2  dir3  document.pdf  file1.txt  file2.log  file_a.txt  file_b.log  image.jpg  report_2023.txt  report_2024.log
```

## 模式匹配（Globbing）

模式匹配，也称为 globbing，允许你根据模式使用称为元字符（metacharacters）的特殊字符来选择文件。

| 元字符        | 匹配内容                             | 示例                                                   |
| :------------ | :----------------------------------- | :----------------------------------------------------- |
| `*`           | 零个或多个字符的任意字符串。         | `*.txt` 匹配所有以 `.txt` 结尾的文件。                 |
| `?`           | 任意单个字符。                       | `file?.txt` 匹配 `file1.txt`、`fileA.txt` 等。         |
| `[abc...]`    | 方括号内任意一个字符。               | `file[12].txt` 匹配 `file1.txt` 或 `file2.txt`。       |
| `[!abc...]`   | 方括号内除指定字符外的任意一个字符。 | `file[!1].txt` 匹配 `fileA.txt` 但不匹配 `file1.txt`。 |
| `[[:alpha:]]` | 任意字母字符。                       | `file[[:alpha:]].txt` 匹配 `fileA.txt`。               |
| `[[:digit:]]` | 任意单个数字（0 到 9）。             | `file[[:digit:]].txt` 匹配 `file1.txt`。               |

让我们在 `data` 目录中尝试一些示例。

1. 使用 `*`：
   列出所有以 `.txt` 结尾的文件：

   ```bash
   ls data/*.txt
   ```

   输出：

   ```plaintext
   data/file1.txt  data/file_a.txt  data/report_2023.txt
   ```

   列出所有名称中包含 `file` 的文件：

   ```bash
   ls data/*file*
   ```

   输出：

   ```plaintext
   data/file1.txt  data/file2.log  data/file_a.txt  data/file_b.log
   ```

2. 使用 `?`：
   列出 `.log` 前恰好有一个字符的文件：

   ```bash
   ls data/file?.log
   ```

   输出：

   ```plaintext
   data/file2.log
   ```

3. 使用 `[]` 进行字符集匹配：
   列出以 `report_` 开头，名称中包含 `2023` 或 `2024` 的文件：

   ```bash
   ls data/report_[2][0][2][34].*
   ```

   输出：

   ```plaintext
   data/report_2023.txt  data/report_2024.log
   ```

4. 使用 `{}` 进行花括号扩展：
   列出以 `file` 开头，并以 `.txt` 或 `.log` 结尾的文件：

   ```bash
   ls data/file*.{txt,log}
   ```

   输出：

   ```plaintext
   data/file1.txt  data/file2.log  data/file_a.txt  data/file_b.log
   ```

## 波浪号扩展 `~`

波浪号（`~`）字符扩展为当前用户的家目录（`/home/labex`）。如果后面跟用户名，它将扩展为该用户的家目录。

```bash
echo ~
```

输出：

```plaintext
/home/labex
```

```bash
echo ~root
```

输出：

```plaintext
/root
```

```bash
echo ~/project/data
```

输出：

```plaintext
/home/labex/project/data
```

## 花括号扩展 `{}`

花括号扩展用于生成任意字符串。它对于创建具有相似名称的文件或目录列表非常有用，而无需单独输入每个名称。

1. 逗号分隔列表：
   创建文件 `report_jan.txt`、`report_feb.txt`、`report_mar.txt`：

   ```bash
   touch data/report_{jan,feb,mar}.txt
   ```

   列出它们：

   ```bash
   ls data/report_*.txt
   ```

   输出：

   ```plaintext
   data/report_2023.txt  data/report_jan.txt  data/report_feb.txt  data/report_mar.txt
   ```

2. 数字或字母范围：
   创建文件 `doc1.txt`、`doc2.txt`、`doc3.txt`：

   ```bash
   touch data/doc{1..3}.txt
   ```

   列出它们：

   ```bash
   ls data/doc*.txt
   ```

   输出：

   ```plaintext
   data/doc1.txt  data/doc2.txt  data/doc3.txt
   ```

   创建目录 `chapterA`、`chapterB`、`chapterC`：

   ```bash
   mkdir data/chapter{A..C}
   ```

   列出它们：

   ```bash
   ls data/chapter*
   ```

   输出：

   ```plaintext
   data/chapterA  data/chapterB  data/chapterC
   ```

## 变量扩展

Shell 变量存储值，这些值可以在命令中扩展。你使用 `VARNAME=value` 定义一个变量，并使用 `$VARNAME` 或 `${VARNAME}` 访问其值。

```bash
MY_DIR=data
echo "My directory is: $MY_DIR"
```

输出：

```plaintext
My directory is: data
```

你可以在命令中使用变量：

```bash
ls $MY_DIR
```

输出将是 `data` 目录的内容。

## 命令替换

命令替换允许你将一个命令的输出用作另一个命令的一部分。这通过将命令用 `$(command)` 或反引号 `` `command` `` 包围来实现。`$(command)` 语法通常更受青睐，因为它支持嵌套。

让我们获取当前日期并将其用于文件名中。

```bash
touch data/log_$(date +%Y-%m-%d).txt
```

列出 `data` 目录以查看新文件：

```bash
ls data/log_*.txt
```

输出将类似于：

```plaintext
data/log_2024-03-07.txt
```

你也可以用它来计算文件数量：

```bash
echo "There are $(ls data | wc -l) items in the data directory."
```

输出将类似于：

```plaintext
There are 19 items in the data directory.
```

## 保护参数免受扩展影响

有时，你希望阻止 Shell 扩展某些字符。你可以通过以下方式实现：

1. **反斜杠（`\`）：** 转义紧随其后的单个字符。

   ```bash
   echo "The value of \$HOME is your home directory."
   ```

   输出：

   ```plaintext
   The value of $HOME is your home directory.
   ```

2. **单引号（`''`）：** 阻止引号内的所有 Shell 扩展。

   ```bash
   echo 'The current date is $(date +%Y-%m-%d).'
   ```

   输出：

   ```plaintext
   The current date is $(date +%Y-%m-%d).
   ```

3. **双引号（`""`）：** 阻止大多数 Shell 扩展，但允许变量扩展（`$VAR`）和命令替换（`$()`）。

   ```bash
   MY_DATE=$(date +%Y-%m-%d)
   echo "Today's date is $MY_DATE."
   ```

   输出：

   ```plaintext
   Today's date is 2024-03-07.
   ```

   与单引号进行比较：

   ```bash
   echo 'Today is $MY_DATE.'
   ```

   输出：

   ```plaintext
   Today's date is $MY_DATE.
   ```

最后，让我们清理 `data` 目录。

```bash
rm -r data
```
