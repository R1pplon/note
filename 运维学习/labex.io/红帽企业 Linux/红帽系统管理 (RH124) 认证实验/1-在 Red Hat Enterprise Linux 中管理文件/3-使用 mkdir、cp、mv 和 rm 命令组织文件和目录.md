# 使用 mkdir、cp、mv 和 rm 命令组织文件和目录

在此步骤中，你将学习如何使用 `mkdir`（创建目录）、`cp`（复制）、`mv`（移动/重命名）和 `rm`（删除）命令来有效地组织文件和目录。这些命令对于维护一个干净且结构化的文件系统至关重要。

首先，确保你位于 `~/project` 目录中。

```bash
cd ~/project
```

验证你的当前位置：

```bash
pwd
```

输出应该是 `/home/labex/project`。

## `mkdir`: 创建目录

你已经使用 `mkdir` 创建过单个目录。现在，让我们探索更高级的 `mkdir` 选项。

你可以通过将多个目录作为参数列出，一次创建多个目录：

```bash
mkdir reports presentations
```

验证它们的创建：

```bash
ls
```

你应该会看到 `documents`、`presentations` 和 `reports` 被列出。

`mkdir` 命令的 `-p`（parents，父目录）选项对于创建目标目录所需的任何缺失的父目录非常有用。这可以防止在你尝试在不存在的父目录中创建子目录时出错。

让我们创建一个嵌套的目录结构：`projects/alpha/docs`。

```bash
mkdir -p projects/alpha/docs
```

现在，使用 `ls -R` 查看新创建的嵌套结构：

```bash
ls -R projects
```

你应该会看到：

```plaintext
projects:
alpha

projects/alpha:
docs

projects/alpha/docs:
```

## `cp`: 复制文件和目录

`cp` 命令用于复制文件和目录。复制文件时，它会在当前目录或指定的其他目录中创建一个副本。

让我们将 `documents` 中的 `report.txt` 复制到 `reports` 目录。

```bash
cp documents/report.txt reports/
```

通过列出 `reports` 目录的内容来验证复制：

```bash
ls reports
```

你应该会在 `reports` 目录中看到 `report.txt`。

如果目标目录中存在同名文件，`cp` 默认会覆盖它。

要复制目录及其内容，你必须使用 `-r`（recursive，递归）选项。默认情况下，如果未指定 `-r`，`cp` 会忽略目录。

让我们将整个 `documents` 目录复制到 `projects/alpha/` 中。

```bash
cp -r documents projects/alpha/
```

验证递归复制：

```bash
ls -R projects/alpha/documents
```

你应该会看到 `documents` 目录的内容（包括 `notes.txt`、`drafts` 和 `drafts/draft_v1.txt`）现在已复制到 `projects/alpha/documents` 中。

```plaintext
projects/alpha/documents:
drafts  notes.txt  report.txt

projects/alpha/documents/drafts:
draft_v1.txt
```

## `mv`: 移动和重命名文件和目录

`mv` 命令将文件从一个位置移动到另一个位置。它还可以用于重命名文件或目录。如果你将文件的绝对路径视为其完整名称，那么移动文件实际上与重命名文件相同。移动文件的内容保持不变。

让我们将 `documents` 目录中的 `notes.txt` 重命名为 `meeting_notes.txt`。

```bash
mv documents/notes.txt documents/meeting_notes.txt
```

验证重命名：

```bash
ls documents
```

你现在应该看到 `meeting_notes.txt` 而不是 `notes.txt`。

现在，让我们将 `reports` 目录中的 `report.txt` 移动到 `documents/drafts` 中。

```bash
mv reports/report.txt documents/drafts/
```

验证移动：

```bash
ls reports
```

`reports` 目录现在应该是空的。

```bash
ls documents/drafts
```

你应该会在 `documents/drafts` 目录中看到 `draft_v1.txt` 和 `report.txt`。

`mv` 的 `-v` 选项会显示命令操作的详细输出，这有助于确认。

```bash
mv -v documents/meeting_notes.txt documents/final_notes.txt
```

你将看到类似以下的输出：

```plaintext
renamed 'documents/meeting_notes.txt' -> 'documents/final_notes.txt'
```

## `rm`: 删除文件和目录

`rm` 命令用于删除文件。请小心使用 `rm`，因为删除的文件通常无法从命令行恢复。

让我们创建一个临时文件来练习 `rm`。

```bash
touch temp_file.txt
```

现在，删除 `temp_file.txt`：

```bash
rm temp_file.txt
```

验证其删除：

```bash
ls
```

`temp_file.txt` 应该不再被列出。

默认情况下，`rm` 不会删除目录。如果你尝试在没有正确选项的情况下删除非空目录，你将收到一个错误。

```bash
rm presentations
```

你将看到一个错误：

```plaintext
rm: cannot remove 'presentations': Is a directory
```

要删除目录及其内容，你必须使用 `-r`（recursive，递归）选项。`rm -r` 命令会先遍历每个子目录，单独删除其文件，然后再删除每个目录。

让我们删除 `presentations` 目录及其内容（目前是空的，但目录仍需要 `-r`）。

```bash
rm -r presentations
```

验证其删除：

```bash
ls
```

`presentations` 应该不再被列出。

`rm` 的 `-i` 选项会在删除每个文件之前交互式地提示用户确认。这是一个很好的安全措施。

让我们创建几个临时文件，然后交互式地删除它们。

```bash
touch file1.txt file2.txt
```

```bash
rm -i file1.txt file2.txt
```

你将为每个文件收到提示：

```plaintext
rm: remove regular empty file 'file1.txt'? y
rm: remove regular empty file 'file2.txt'? y
```

为每个提示输入 `y` 并按 Enter 以确认删除。

`-f` 选项（force，强制）会强制删除而不提示用户确认。如果你同时指定了 `-i` 和 `-f`，则 `-f` 具有优先权。**请极其谨慎地使用 `-f`。**

最后，让我们清理 `projects` 目录。

```bash
rm -r projects
```

验证 `projects` 是否已消失：

```bash
ls
```

## `rmdir`: 删除空目录

你也可以使用 `rmdir` 命令来删除空目录。如果目录非空，它将失败。

让我们创建一个空目录并使用 `rmdir` 删除它。

```bash
mkdir empty_dir
```

```bash
rmdir empty_dir
```

验证其删除：

```bash
ls
```

`empty_dir` 应该不再被列出。

如果你尝试使用 `rmdir` 删除非空目录，它将失败：

```bash
mkdir test_dir
touch test_dir/test_file.txt
rmdir test_dir
```

你将看到一个错误：

```plaintext
rmdir: failed to remove 'test_dir': Directory not empty
```

请记住，对于非空目录，请使用 `rm -r`。

```bash
rm -r test_dir
```
