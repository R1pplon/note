# 使用 cd 和 touch 命令导航目录和创建文件

在此步骤中，你将学习如何使用 `cd` 命令更改当前工作目录，以及使用 `touch` 命令创建空文件。这些命令对于高效地组织文件和在文件系统中导航至关重要。

首先，让我们验证你的当前位置，并导航到步骤 1 中显示的 `project` 目录。

```bash
pwd
```

你应该会看到 `/home/labex` 作为输出（你的主目录）。

现在，让我们使用 `cd` 命令导航到 `project` 目录：

```bash
cd project
```

验证你的新位置：

```bash
pwd
```

你现在应该看到 `/home/labex/project` 作为输出。

现在，让我们在当前 `project` 目录中创建一个名为 `documents` 的新目录。你将使用 `mkdir` 命令，该命令在上一
步中已简要提及。

```bash
mkdir documents
```

验证 `documents` 目录是否已创建：

```bash
ls
```

你应该会在输出中看到 `documents`。

接下来，你将使用 `cd` 命令将 shell 的当前工作目录更改为新创建的 `documents` 目录。

```bash
cd documents
```

更改目录后，使用 `pwd` 确认你的新位置是一个好习惯：

```bash
pwd
```

输出现在应该是 `/home/labex/project/documents`。

`cd` 命令提供了几个方便的导航选项：

1. `cd -`：此命令会切换到你之前所在的目录。让我们试试。

   ```bash
   cd -
   ```

   你应该会回到 `/home/labex/project`。使用 `pwd` 进行验证：

   ```bash
   pwd
   ```

   现在，再次使用 `cd -` 返回到 `/home/labex/project/documents`：

   ```bash
   cd -
   ```

   并使用 `pwd` 进行验证：

   ```bash
   pwd
   ```

2. `cd ..`：此命令使用 `..`（两个点）隐藏目录向上移动一个级别到父目录，而无需知道确切的父目录名称。

   ```bash
   cd ..
   ```

   你现在应该在 `/home/labex/project` 中。使用 `pwd` 进行验证：

   ```bash
   pwd
   ```

   你可以链接 `..` 来向上移动多个级别。例如，要从 `/home/labex/project` 进入 `/home/labex`：

   ```bash
   cd ../
   ```

   验证你的位置：

   ```bash
   pwd
   ```

   你应该在 `/home/labex` 中。

3. `cd`（不带任何参数）：此命令将始终带你回到你的主目录，对于 `labex` 用户来说，这是 `/home/labex`。

   ```bash
   cd
   ```

   验证你的位置：

   ```bash
   pwd
   ```

   你应该在 `/home/labex` 中。

现在，让我们导航回你的 `~/project/documents` 目录以创建一些文件。

```bash
cd ~/project/documents
```

验证你的位置：

```bash
pwd
```

输出应该是 `/home/labex/project/documents`。

你现在将使用 `touch` 命令。`touch` 命令会更新文件的 timestamp 为当前日期和时间，而不会修改其他任何内容。此命令对于创建空文件也非常有用。

让我们在 `~/project/documents` 目录中创建两个名为 `report.txt` 和 `notes.txt` 的空文件。

```bash
touch report.txt notes.txt
```

使用 `ls -l` 验证文件是否已创建：

```bash
ls -l
```

你应该会看到类似以下的输出，显示新创建的文件：

```plaintext
total 0
-rw-rw-r--. 1 labex labex 0 Mar  7 HH:MM report.txt
-rw-rw-r--. 1 labex labex 0 Mar  7 HH:MM notes.txt
```

最后，让我们在 `~/project/documents` 中创建一个名为 `drafts` 的新目录，然后在其内部创建一个文件。

```bash
mkdir drafts
```

```bash
cd drafts
```

```bash
touch draft_v1.txt
```

验证文件创建：

```bash
ls -l
```

你应该会看到 `draft_v1.txt` 被列出。

现在，使用一个 `cd` 命令导航回你的 `~/project` 目录。

```bash
cd ~/project
```

验证你的位置：

```bash
pwd
```

输出应该是 `/home/labex/project`。
