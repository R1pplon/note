[RedHat红帽Linux系统管理员认证(RHCSA-EX200)](https://www.udemy.com/course/rhcsa-ex200/)

## 课程介绍和环境准备

课程介绍
红帽开发者账号和系统镜像下载
视频播放器设置
Windows + VirtualBox创建RHEL虚拟机
安装特别注意！
MacOS + VirtualBox创建RHEL虚拟机（Intel芯片）
关于鼠标的捕获VirtualBox
VMware Fusion在MacOs上的安装
MacOS + VMware创建RHEL虚拟机（M系列芯片）
SSH远程链接RHEL虚拟机
使用VirtualBox + Vagrant快速创建RHEL虚拟机
Vagrant入门（可选）
理解Linux的Command

## 文件相关的基本操作和文件管理

命令行的重定向Redirection
文件编辑器nano和vim
文件编辑器nano演示
文件编辑器vim演示
Directory的基本操作 - cd pwd mkdir rm
文件的基本操作 - cp mv rm
文件的读取- cat more less head tail
关于正则表达式的学习
文件的搜索- grep
grep练习
用户的创建和切换
文件的权限介绍（1）
文件的权限介绍（2）
默认权限和umask命令
文件权限的修改Permission
文件的所有属性修改Ownership
Access Control List (ACL)
文件的打包tar
文件的压缩gzip/bzip2
tar命令同时打包和压缩

## 管理用户和组 Users and Groups

用户的查看和创建useradd
用户的编辑和删除usermod/userdel/newgrp
给用户设置密码passwd
shadow文件介绍getent
用户password如何验证openssl passwd
修改密码passwd/chpasswd
管理用户的组groupadd/usermod/gpasswd
Using su
Using sudo

## Deploying, Configuring and Maintaining Systems

yum还是dnf？
yum的基本用法
常见yum失败解决办法
Security Update 安全更新
NTP和Chrony
补充 - 查看当前时区和可用时区
chronyd和chronyc

## Scheduling Jobs 计划任务

工作调度器 Scheduler
使用at设置计划任务
使用cron设置计划任务
Systemd Timer Unit
Systemd Timer Unit演示

## Systemd 完整入门

systemd完全入门

## Managing Networking 管理网络

IP Command 介绍
配置和管理IP地址
ARP是什么
ARP Cache缓存的查看和管理
IP命令笔记
网络命名空间 Namespace
关于静态路由
网络相关配置文件
添加一个新的网络接口
DNS相关配置
Linux防火墙介绍
firewall-cmd查看firewall rule
firewall-cmd添加service
firewall-cmd添加自定义service
firewall-cmd zones

## Managing Security

基于SSH Key的认证
SSH config文件
禁止SSH用户名密码登录
Fail2ban保护SSH的安全

## Operating Running Systems

shutdown system
限制普通用户登录
reboot and poweroff
uptime和top
Process的查看和管理
Process Priority
系统调优
系统syslog的查看

## Getting Started with Podman

本章介绍
Podman是什么？
Podman的安装
Podman 安装命令
Podman Images
Image从哪里下载的
Image Build
Running Container
Running Container in the Background
Naming Container
Monitoring Containers
Port Mapping端口映射
Volumes Mapping
systemd控制podman Container
podman bridge network
podman customer network
podman-compose
关于容器的继续学习

## Creating Shell Scripts

本章介绍
exit code
直接在CLI里写shell script
and / or 逻辑
shell script的执行方法
shebang是什么
shell script如何传入参数
脚本演示—需求
脚本演示—检查用户输入
脚本演示—检查用户是否已存在
脚本演示—密码输入
脚本演示—用户创建
for loop
创建function

## bonus

"手稿"
