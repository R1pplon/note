# 创建用于协作的 Set-GID 目录

在此任务中，你需要在 `~/project` 目录下创建一个名为 `collaboration` 的新目录，并为其设置 Set-GID（设置组 ID）权限位。

## 任务

- 在 `~/project` 目录下创建一个名为 `collaboration` 的新目录。
- 为 `collaboration` 目录设置 Set-GID（设置组 ID）权限位。

## 要求

- 新目录必须位于 `~/project/collaboration`。
- 目录名称必须准确为 `collaboration`。
- 必须在 `~/project/collaboration` 目录上正确应用 Set-GID 权限位。

## 示例

完成任务后，使用 `ls -ld ~/project/collaboration` 检查 `collaboration` 目录的权限，输出结果应类似于下方内容。请注意属组权限部分中的 `s`，这表示 Set-GID 位已激活。

```bash
ls -ld ~/project/collaboration
```

```plaintext
drwxrwsr-x. 2 labex labex 6 Apr 12 12:34 collaboration
```
