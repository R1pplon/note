# while 循环

只要指定的条件为真（true），`while` 循环就会执行一段代码块。这就像在说：「当此条件为真时，请继续执行此操作。」

创建一个名为 `while_loop.sh` 的新文件：

```bash
touch while_loop.sh
```

在 WebIDE 中打开 `while_loop.sh` 文件并添加以下内容：

```bash
#!/bin/bash

# 使用 while 循环进行简单的倒计时
count=5
echo "Countdown:"
while [ $count -gt 0 ]; do
  echo $count
  count=$((count - 1))
  sleep 1 # 等待 1 秒
done
echo "Blast off!"
```

让我们分解这个脚本：

1. 我们从 `count=5` 开始，设置初始倒计时值。
2. 条件 `[ $count -gt 0 ]` 表示「当 count 大于 0 时」。
3. 在循环内部，我们打印当前的 count 值，将其减 1，然后等待一秒钟。
4. 这一过程会一直持续到 count 达到 0，此时循环结束。

`sleep 1` 命令会让脚本暂停 1 秒钟，从而产生实时的倒计时效果。

保存文件并使其可执行：

```bash
chmod +x while_loop.sh
```

现在，运行脚本：

```bash
./while_loop.sh
```

你会看到从 5 到 1 的倒计时，每个数字之间有一秒钟的停顿：

```
Countdown:
5
4
3
2
1
Blast off!
```

这演示了 `while` 循环如何重复执行操作，直到条件不再满足。
