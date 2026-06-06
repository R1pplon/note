# 使用 Reflog 恢复丢失的提交

现在，如果你意识到你并不想丢弃那个除法函数该怎么办？这就是 `git reflog` 大显身手的地方。引用日志（reflog）是本地仓库中 HEAD 经过的所有位置的记录。它就像一个超级历史记录，甚至记录了像 reset 这样重写历史的命令。

让我们查看引用日志：

```bash
git reflog
```

你应该会看到你最近采取的所有操作列表，包括你的重置操作。每个条目都有一个 `HEAD@{n}` 标识符。

要找回丢失的提交，你可以重置到硬重置之前的状态：

```bash
git reset --hard HEAD@{1}
```

这会重置到 HEAD 在你执行上一个动作（即硬重置）之前的状态。

现在检查 `math.js`，你应该会看到除法函数回来了！

```bash
cat math.js
```

```
function add(a, b) { return a + b; }
function subtract(a, b) { return a - b; }
function multiply(a, b) { return a * b; }
function divide(a, b) { return a / b; }
```

引用日志是一个强大的安全网，允许你从几乎任何 Git 意外中恢复。但是，请记住它是你机器本地的，并且是临时的（条目通常保留 30 到 90 天）。它不能替代定期备份或将工作推送到远程仓库。
