# 配置本地 Git 工具

在此步骤中，你将注册一个名为 `Local Git` 的 Jenkins Git 工具。工具配置允许 Jenkins 引用已命名的安装路径，而不是猜测使用哪个可执行文件。

在 Jenkins 控制面板中：

点击 `Manage Jenkins`，然后点击 `Tools`。

滚动到 `Git installations` 部分。如果已经存在 Git 安装配置，请更新第一个。如果该部分没有安装配置，请点击 `Add Git`。

使用以下值填写 Git 安装信息：

- `Name`: `Local Git`
- `Path to Git executable`: `git`

点击 `Save`。

Jenkins 会将此工具定义存储在其控制器配置中。

![Jenkins 本地 Git 工具](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-configuring-tools-and-labeled-agents/zh/../assets/step3-local-git-tool.png)

运行以下命令以检查已保存的 Git 工具配置：

```bash
docker exec jenkins sh -lc "grep -n -E '<name>Local Git</name>|<home>git</home>' /var/jenkins_home/hudson.plugins.git.GitTool.xml" | tee /home/labex/project/git-tool-lines.txt
```

输出应包含工具名称和可执行文件路径：

```text
...<name>Local Git</name>
...<home>git</home>
```
