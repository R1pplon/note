[Windows Subsystem for Linux 文档 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/)

安装不再赘述

## 确认当前状态

```cmd
查看wsl版本
wsl -v # wsl --version

wsl帮助
wsl --help

# 查看所有发行版、版本和状态
wsl -l -v # 或 wsl --list --verbose

# 只看正在运行的
wsl -l --running

# 只看名称（适合脚本用）
wsl -l -q
```

## 查找「可安装」的发行版

```cmd
# 官方推荐的查看方式
wsl --list --online 
# 简写
wsl -l -o
```

## 安装发行版

```cmd
# 安装指定发行版
wsl --install -d <NAME>
# 不指定的话，默认装 Ubuntu
wsl --install

# `--location` 指定安装目录
wsl --install -d Ubuntu-24.04 --location D:\WSL\Ubuntu-24.04
```

## 导入导出