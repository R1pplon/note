# 使用 Vim 基础编辑文本文件

在这一步中，你将学习 Vim 的基本操作，Vim 是 Linux 环境中一个强大且广泛使用的文本编辑器。Vim 在不同的模式下运行，这对于初学者来说可能有点挑战性，但掌握基础知识将显著提高你的工作效率。

Vim 是一个模式编辑器，这意味着它具有针对不同任务的不同模式：

- **普通模式（命令模式）**：这是你打开 Vim 时的默认模式。在此模式下，按键被解释为命令（例如，移动光标、删除文本、复制文本）。
- **插入模式**：在此模式下，你键入的任何内容都会插入到文件中。你通过按 `i`（在光标处插入）、`a`（在光标后追加）、`o`（在下方打开新行）等从普通模式进入插入模式。要返回普通模式，请按 `Esc`。
- **可视模式**：此模式允许你选择文本块以进行复制、剪切或删除等操作。你通过按 `v`（字符级）、`Shift+V`（行级）或 `Ctrl+V`（块级）从普通模式进入可视模式。按 `Esc` 返回普通模式。
- **命令行模式（Ex 模式）**：此模式用于执行通常以冒号（`:`）开头的命令，例如保存（`:w`）、退出（`:q`）或搜索（`/`）。你通过按 `:` 从普通模式进入此模式。

### 打开和基本导航

1. 确保你在你的 `~/project` 目录中。

    ```bash
    cd ~/project
    ```

    ```plaintext
    [labex@host project]$
    ```

2. 使用 `vim` 打开一个名为 `my_document.txt` 的新文件。

    ```bash
    vim my_document.txt
    ```

    你的终端现在将显示 Vim 界面。你处于**普通模式**。

3. 在普通模式下，你可以使用箭头键或 `h`（左）、`j`（下）、`k`（上）、`l`（右）进行导航。由于文件是空的，所以还没有太多可以导航的内容。

### 插入模式：添加文本

1. 要开始输入，你需要进入**插入模式**。按 `i`（用于插入）。
    你应该在终端的左下角看到 `-- INSERT --`，这表示你处于插入模式。

2. 键入以下几行：

    ```
    This is the first line.
    This is the second line.
    This is the third line.
    ```

3. 要退出插入模式并返回普通模式，请按 `Esc` 键。
    `-- INSERT --` 指示符应该消失。

### 保存和退出

1. 在普通模式下，要保存文件，请键入 `:w` 并按 `Enter`。

    ```
    :w
    ```

    你应该在底部看到 `my_document.txt` [New] `3L, 60B written`，确认已保存。

2. 要退出 Vim，请键入 `:q` 并按 `Enter`。

    ```
    :q
    ```

    你将返回到你的 shell 提示符。

3. 使用 `cat` 验证 `my_document.txt` 的内容。

    ```bash
    cat my_document.txt
    ```

    ```plaintext
    This is the first line.
    This is the second line.
    This is the third line.
    ```

### 编辑现有文件

1. 再次打开 `my_document.txt`。

    ```bash
    vim my_document.txt
    ```

2. 在普通模式下，将你的光标移动到第二行的开头（使用 `j` 或箭头键）。

3. 按 `Shift+V` 进入**可视行模式**。整行将被高亮显示。

4. 按 `y` “yank”（复制）选定的行。

5. 将你的光标移动到第三行的末尾（使用 `j` 或箭头键）。

6. 按 `p` “put”（粘贴）yank 的行到当前行的下方。
    第二行现在将再次显示为第四行。

7. 现在，让我们删除一行。将你的光标移动到第四行（你刚刚粘贴的那一行）。

8. 按 `dd`（双击 `d`）删除整行。

9. 要撤消你的最后一次更改，请按 `u`。删除的行将重新出现。

10. 要在一个命令中保存并退出，请键入 `:wq` 并按 `Enter`。

    ```
    :wq
    ```

11. 再次验证 `my_document.txt` 的内容。

    ```bash
    cat my_document.txt
    ```

    ```plaintext
    This is the first line.
    This is the second line.
    This is the third line.
    This is the second line.
    ```

    该文件现在应该有四行，第二行被复制了。

### 放弃更改

有时你进行更改并决定不想保存它们。

1. 再次打开 `my_document.txt`。

    ```bash
    vim my_document.txt
    ```

2. 通过按 `i` 进入插入模式。

3. 在末尾添加一个新行：

    ```
    This line should not be saved.
    ```

4. 按 `Esc` 返回普通模式。

5. 尝试使用 `:q` 退出。

    ```
    :q
    ```

    Vim 将警告你：`E37: No write since last change (add ! to override)`。这意味着你未保存更改。

6. 要退出而不保存，请键入 `:q!` 并按 `Enter`。

    ```
    :q!
    ```

    你将返回到 shell 提示符，并且你的更改将被放弃。

7. 验证 `my_document.txt` 的内容。

    ```bash
    cat my_document.txt
    ```

    ```plaintext
    This is the first line.
    This is the second line.
    This is the third line.
    This is the second line.
    ```

    你添加的最后一行不应存在。

你现在已经涵盖了 Vim 中最基本的操作：打开文件、插入文本、导航、保存、退出和放弃更改。这些是开始使用 Vim 的基本技能。
