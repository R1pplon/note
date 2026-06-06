# 调查 Git 仓库

在开始之前，请注意系统已在 `~/project/mystery-repo` 为你准备好了一个 Git 仓库。该仓库包含一系列提交记录，其中就包括我们的神秘函数消失的那次提交。你的工作区已经初始化完毕，随时可以开始调查。

## 任务

1. 进入位于 `~/project/mystery-repo` 的神秘仓库。
2. 使用 Git 日志命令，找出函数 `secretAlgorithm()` 从 `secret.js` 文件中被删除时所在的提交。
3. 找到负责删除该函数的作者。

## 要求

- 你必须使用带有合适选项的 `git log` 命令来完成此挑战。
- 所有操作都应在 `~/project/mystery-repo` 目录中进行。
- 你需要找到删除 `secretAlgorithm()` 函数的确切提交哈希值（Commit Hash）。
- 你必须识别出执行该删除操作的提交作者。
- 将提交哈希值写入名为 `~/user_answer.txt` 的文件中。
- 将作者姓名写入名为 `~/user_author.txt` 的文件中。

## 示例

成功完成挑战后，你应该能够提供类似以下的信息：

```bash
cat ~/user_answer.txt
```

> 请注意，文件必须放置在 `~` 目录下。

```
a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9
```

```bash
cat ~/user_author.txt
```

```
Jane Doe
```
