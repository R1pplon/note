# 访问 Jenkins 并创建新用户

在此步骤中，我们将访问 Jenkins Web 界面并创建一个新的用户账户。此过程对于向 Jenkins 环境添加团队成员或其他相关人员至关重要。

首先，让我们访问 Jenkins：

1. 从虚拟机桌面打开 Firefox。浏览器会自动打开 `http://localhost:8080`，因此无需手动输入 URL。无需登录。

现在你已进入 Jenkins 控制面板，让我们创建一个新用户：

2. 点击 Jenkins 控制面板右上角的齿轮图标，打开 **Manage Jenkins**（管理 Jenkins）。
3. 点击 **Users**（用户）。这将打开用户管理区域。
4. 点击左侧边栏中的 **Create User**（创建用户）。
5. 填写以下详细信息：

   - Username（用户名）：`labex`（这是用户的唯一标识符）
   - Password（密码）：`123456`（出于安全考虑，在实际场景中应使用更强的密码）
   - Confirm password（确认密码）：`123456`（再次输入密码以确认）
   - Full name（全名）：`Lab User`（此名称将显示在 Jenkins 中）
   - E-mail address（电子邮件地址）：`labex@example.com`（用于接收通知）

6. 仔细检查输入的所有信息，然后点击 **Create User**（创建用户）。

![创建 Jenkins 用户](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-jenkins-user-management/zh/../assets/step1-create-user.png)

创建用户后，你应该能在“管理用户”页面看到新创建的 `labex` 用户。如果没有看到，请尝试刷新页面。
