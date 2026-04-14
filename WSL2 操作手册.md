[Windows Subsystem for Linux 文档 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/)

安装不再赘述

## 设置 WSL 版本


```PowerShell
wsl --set-version <distribution name> <versionNumber>
```

若要指定 Linux 发行版运行的 WSL 版本（1 或 2），请将 `<distribution name>` 替换为发行版的名称，并将 `<versionNumber>` 替换为 1 或 2。

```PowerShell
# 设置默认 WSL 版本为 2
wsl --set-default-version 2
```

## 确认当前状态

```PowerShell
# 查看wsl版本
wsl -v # wsl --version

# wsl帮助
wsl --help

# 查看所有发行版、版本和状态
wsl -l -v # 或 wsl --list --verbose

# 只看正在运行的
wsl -l --running

# 只看名称（适合脚本用）
wsl -l -q
```

## 查找「可安装」的发行版

```PowerShell
# 官方推荐的查看方式
wsl --list --online 
# 简写
wsl -l -o
```

## 安装发行版

```PowerShell
# 安装指定发行版
wsl --install -d <发行版名称>
# 不指定的话，默认装 Ubuntu
wsl --install

# `--location` 指定安装目录
wsl --install -d Ubuntu-24.04 --location D:\WSL\Ubuntu-24.04

# 设置默认发行版
wsl --set-default <发行版名称>
```

## 运行

```PowerShell
# 运行默认发行版
wsl

# 运行指定发行版
wsl -d <发行版名称>

# 停止指定发行版
wsl -t <发行版名称>

# 以特定用户身份运行
wsl --user <Username>

# 更改分发版的默认用户
<发行版名称> config --default-user <Username>
```

## 导出分发

```PowerShell
# 确保已关闭
wsl --shutdown

# 导出 (默认为 tar 格式)
wsl --export <发行版名称> D:\WSL2\backup\ubuntu-24.04.tar
# 也可用 `--vhd` 导出为 .vhdx 文件
```

## 导入发行版

```PowerShell
wsl --import <发行版名称> <InstallLocation> <FileName>

# 示例
wsl --import Ubuntu-24.04 D:\WSL2\Ubuntu-24.04 D:\WSL2\backup\ubuntu-24.04.tar --version 2
```
- `--vhd`：指定导入分发应为 .vhdx 文件而不是 tar 文件（仅使用 WSL 2 支持）
- `--version <1/2>`：指定是否将分发导入为 WSL 1 还是 WSL 2