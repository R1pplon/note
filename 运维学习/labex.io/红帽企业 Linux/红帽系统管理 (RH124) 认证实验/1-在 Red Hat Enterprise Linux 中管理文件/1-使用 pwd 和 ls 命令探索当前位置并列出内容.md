# 使用 pwd 和 ls 命令探索当前位置并列出内容

在此步骤中，你将学习如何使用 `pwd` 和 `ls` 命令来探索文件系统中的当前位置并列出其内容。了解当前工作目录以及查看其内容是使用命令行进行文件导航和管理的基本技能。

首先，让我们使用 `pwd` 命令确定你的当前工作目录。`pwd` 代表“print working directory”（打印工作目录），它会显示你在文件系统中的当前位置的完整路径名。

```bash
pwd
```

你应该会看到类似以下的输出，指示你的当前目录：

```plaintext
/home/labex
```

接下来，你将使用 `ls` 命令列出当前目录的内容。`ls` 代表“list”（列出），它会列出指定目录的内容，或者在未指定目录时列出当前工作目录的内容。

```bash
ls
```

你应该会看到显示你的主目录内容的输出：

```plaintext
project
```

`ls` 命令有几个有用的选项，可以显示有关文件和目录的更多信息。

1. `-l` 选项（长列表格式）提供有关每个文件和目录的详细信息，包括权限、硬链接数、所有者、组、大小和最后修改时间。

   ```bash
   ls -l
   ```

   你应该会看到类似以下的输出：

   ```plaintext
   total 0
   drwxr-xr-x 2 labex labex 6 Aug 27  2024 project
   ```

2. `-a` 选项（所有文件）会列出所有文件，包括隐藏文件。在 Linux 中，以点 (`.`) 开头的文件名被视为隐藏文件。此外，`.` 指代当前目录，`..` 指代父目录。

   ```bash
   ls -a
   ```

   你应该会看到类似以下的输出，包括隐藏的配置文件：

   ```plaintext
   .  ..  .bash_history  .bash_logout  .bash_profile  .bashrc  .tmux.conf  project
   ```

3. 将 `-l` 和 `-a` 合并为 `-la`，可以获得所有文件（包括隐藏文件）的长列表。

   ```bash
   ls -la
   ```

   这将显示所有文件和目录的详细信息，包括隐藏文件：

   ```plaintext
   total 20
   drwx------ 1 labex labex  72 Jun  4 03:12 .
   drwxr-xr-x 1 root  root   19 Aug 27  2024 ..
   -rw------- 1 labex labex  31 Jun  4 03:13 .bash_history
   -rw-r--r-- 1 labex labex  18 Feb 15  2024 .bash_logout
   -rw-r--r-- 1 labex labex 141 Feb 15  2024 .bash_profile
   -rw-r--r-- 1 labex labex 877 Jun  4 03:12 .bashrc
   -rw-r--r-- 1 labex labex  68 Aug 27  2024 .tmux.conf
   drwxr-xr-x 2 labex labex   6 Aug 27  2024 project
   ```

4. `-R` 选项（递归）会递归地列出所有子目录的内容。为了演示这一点，我们首先创建一个子目录。

   ```bash
   mkdir mydir
   ```

   现在，使用 `ls -R` 查看递归列表。

   ```bash
   ls -R
   ```

   你应该会看到 `mydir` 和 `project` 目录及其内容被列出：

   ```plaintext
   .:
   mydir  project

   ./mydir:

   ./project:
   ```

   最后，让我们删除 `mydir` 目录，以便为后续步骤清理环境。

   ```bash
   rmdir mydir
   ```

   此命令仅适用于空目录。你将在后续步骤中了解更多关于 `rmdir` 和 `rm` 的信息。

![Command line ls output example](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/rhel/rh124/manage-files-in-red-hat-enterprise-linux/zh/../assets/20250604-11-14-19-3J8HoLfF.png)
