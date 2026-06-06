# 使用 git diff 查看更改

随着项目的增长，你经常会想在提交之前回顾一下所做的更改。Git 为此提供了一个强大的命令：`git diff`。

让我们修改 `hello.py` 文件：

```bash
echo "print('Hello, Git! Welcome to the staging area.')" > hello.py
```

现在，让我们使用 `git diff` 来查看发生了什么变化：

```bash
git diff
```

你应该会看到类似这样的输出：

```diff
diff --git a/hello.py b/hello.py
index ed51d3f..1385fe3 100644
--- a/hello.py
+++ b/hello.py
@@ -1 +1 @@
-print('Hello, Git!')
+print('Hello, Git! Welcome to the staging area.')
```

这段输出准确地显示了文件中更改的内容。以 `-` 开头的行显示了被删除的内容，以 `+` 开头的行显示了新添加的内容。

`git diff` 就像时光机的日志本。它让你看到自己做出的确切改动，当你试图回忆做了什么，或者在提交前进行代码审查时，这非常有用。`git diff --staged` 是该命令的另一个常用变体，它显示你已经放入暂存区但尚未提交的更改。这在你准备最终提交前进行最后检查时非常有帮助。

按下 `q` 键退出 `git diff` 视图。
