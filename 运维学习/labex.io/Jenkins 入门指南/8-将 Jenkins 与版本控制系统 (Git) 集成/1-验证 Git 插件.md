# 验证 Git 插件

Jenkins 使用插件来添加集成功能。Git 插件提供了你在作业配置中将要使用的 **Git** 选项。

1. 从桌面界面打开 Firefox 浏览器。浏览器会自动打开 `http://localhost:8080`，无需手动输入 URL，也无需登录。
2. 在 Jenkins 仪表板上，点击右上角的齿轮图标以打开 **Manage Jenkins**（管理 Jenkins）。
3. 点击 **Plugins**（插件）。
4. 选择 **Installed plugins**（已安装插件）选项卡。
5. 在过滤框中输入 `git`。
6. 确认 **Git** 出现在已安装插件列表中。

![已安装的 Git 插件](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-integrating-jenkins-with-source-control-git/zh/../assets/step1-installed-git-plugin.png)

确认 Git 插件后，你就可以创建一个从仓库拉取源代码的项目了。
