# WebIDE (VS Code) 简介

在本次 Shell 脚本课程中，我们将使用基于 Visual Studio Code (VS Code) 的 WebIDE。这个集成开发环境提供了一个便捷的平台，让你可以在同一个地方编辑脚本并访问终端。

需要注意的是，虽然 WebIDE 中的默认 Shell 是 Zsh，但我们在本实验中将编写 Bash 脚本。Bash 是类 Unix 系统中最常见且使用最广泛的 Shell。Zsh 是 Bash 的扩展版本，具有一些改进和新特性。就本实验而言，这些差异不会影响我们的工作，因为我们的脚本将通过 Shebang 行（`#!/bin/bash`）明确指定使用 Bash。

## 访问 WebIDE

当你开始实验时，你会在浏览器中看到 WebIDE 界面。它由几个关键部分组成：

1. 文件资源管理器（左侧边栏）：显示目录结构和文件。
2. 编辑器（主区域）：你编写和编辑脚本的地方。
3. 终端（底部面板）：你运行命令和执行脚本的地方。

## 打开终端

要在 WebIDE 中打开终端：

1. 点击顶部菜单中的「Terminal」。
2. 从下拉菜单中选择「New Terminal」。

这将在 WebIDE 底部打开一个新的终端面板。你会看到一个类似于这样的命令提示符：

```
labex:project/ $
```

这表示你当前以 `labex` 用户身份登录，当前目录是 `~/project`（这是 `/home/labex/project` 的简写）。

## 使用终端

![WebIDE 终端界面示例](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/shell/learn-shell/lab-01-hello-world/zh/../assets/screenshot-20240816-gnuaVty3@2x.png)

你可以像在普通 Linux 系统上一样使用这个终端。例如，要查看当前目录的内容，可以输入：

```bash
ls
```

然后按回车键。这将列出你当前位置的所有文件和目录。
