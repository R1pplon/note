安装不再赘述

## 确认当前状态

```cmd
查看wsl版本
wsl -v # wsl --version

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
```