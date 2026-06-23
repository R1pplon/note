系统信息

> OS 名称:          Microsoft Windows 10 IoT 企业版 LTSC
> OS 版本:          10.0.19044 暂缺 Build 19044

## 文件准备

- e53e159d00e04f729cc2180cffd1c02e_License1.xml
- Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle
- Microsoft.UI.Xaml.2.8.appx
- Microsoft.VCLibs.140.00_14.0.33519.0_x64.appx
- Microsoft.VCLibs.140.00.UWPDesktop_14.0.33728.0_x64.appx
- WindowsAppRuntimeInstall-x64.exe

https://github.com/microsoft/winget-cli/releases/
获取
- Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle
- e53e159d00e04f729cc2180cffd1c02e_License1.xml

https://www.nuget.org/packages/Microsoft.UI.Xaml/
下载 `.nupkg` 后把扩展名改为 `.zip`，解压取出 `tools\AppX\x64\Release\Microsoft.UI.Xaml.2.8.appx`
得到
- Microsoft.UI.Xaml.2.8.appx

https://github.com/harryeffinpotter/PC-Gaming-Redists/raw/main/Microsoft.VCLibs.140.00_14.0.33519.0_x64.appx
得到
- Microsoft.VCLibs.140.00_14.0.33519.0_x64.appx

https://github.com/harryeffinpotter/PC-Gaming-Redists/raw/main/Microsoft.VCLibs.140.00.UWPDesktop_14.0.33728.0_x64.appx
得到
- Microsoft.VCLibs.140.00.UWPDesktop_14.0.33728.0_x64.appx

[Windows 应用 SDK 早期版本下载 - Windows apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/apps/windows-app-sdk/downloads-archive)
获取版本 **1.8.9 (1.8.260529003)**
下载链接 https://aka.ms/windowsappsdk/1.8/1.8.260529003/windowsappruntimeinstall-x64.exe
得到
- WindowsAppRuntimeInstall-x64.exe

## 安装命令

管理员权限执行

```PowerShell
Add-AppxPackage -Path "./Microsoft.UI.Xaml.2.8.appx"
Add-AppxPackage -Path "./Microsoft.VCLibs.140.00.UWPDesktop_14.0.33728.0_x64.appx"
.\WindowsAppRuntimeInstall-x64.exe
Add-AppxPackage -Path "./Microsoft.VCLibs.140.00_14.0.33519.0_x64.appx"
Add-AppxPackage -Path "./Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle"
Add-AppxProvisionedPackage -Online -PackagePath "./Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle"  -LicensePath "./e53e159d00e04f729cc2180cffd1c02e_License1.xml"
```



系统信息

> OS 名称:          Microsoft Windows 10 IoT 企业版 LTSC
> OS 版本:          10.0.19044 暂缺 Build 19044

### 一、 文件准备

1. **winget 主程序及许可证**
   - 来源：[GitHub Release](https://github.com/microsoft/winget-cli/releases/)
   - 文件：
     - `Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle`
     - `e53e159d00e04f729cc2180cffd1c02e_License1.xml`
2. **UI.Xaml 2.8 框架**
   - 来源：[NuGet](https://www.nuget.org/packages/Microsoft.UI.Xaml/) 下载 `.nupkg` 后改扩展名为 `.zip`，解压取出 `tools\AppX\x64\Release\` 目录下的文件。
   - 文件：`Microsoft.UI.Xaml.2.8.appx`
   - 直链：
       - https://www.nuget.org/api/v2/package/Microsoft.UI.Xaml/2.8.7
3. **VCLibs 框架 (x64)**
   - 直链：
     - https://github.com/harryeffinpotter/PC-Gaming-Redists/raw/main/Microsoft.VCLibs.140.00_14.0.33519.0_x64.appx
     - https://github.com/harryeffinpotter/PC-Gaming-Redists/raw/main/Microsoft.VCLibs.140.00.UWPDesktop_14.0.33728.0_x64.appx
   - 文件：
     - `Microsoft.VCLibs.140.00_14.0.33519.0_x64.appx`
     - `Microsoft.VCLibs.140.00.UWPDesktop_14.0.33728.0_x64.appx`
4. **Windows App Runtime 1.8**
  -    
   - 直链：https://aka.ms/windowsappsdk/1.8/1.8.260529003/windowsappruntimeinstall-x64.exe
   - 文件：`WindowsAppRuntimeInstall-x64.exe`
### 二、 执行安装命令
在该文件夹下，**以管理员权限打开 PowerShell**，直接依次粘贴执行以下命令：
```PowerShell
# 1. 安装底层依赖框架
Add-AppxPackage -Path "./Microsoft.UI.Xaml.2.8.appx"
Add-AppxPackage -Path "./Microsoft.VCLibs.140.00.UWPDesktop_14.0.33728.0_x64.appx"
# 2. 安装 Windows App Runtime 1.8
.\WindowsAppRuntimeInstall-x64.exe
# 3. 安装 VCLibs 主框架
Add-AppxPackage -Path "./Microsoft.VCLibs.140.00_14.0.33519.0_x64.appx"
# 4. 安装 winget 主程序
Add-AppxPackage -Path "./Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle"
# 5. 配置系统级许可证（解决 LTSC 无法运行应用问题）
Add-AppxProvisionedPackage -Online -PackagePath "./Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle" -LicensePath "./e53e159d00e04f729cc2180cffd1c02e_License1.xml"
```
### 三、 验证
重启 PowerShell 窗口，输入 `winget --version`，输出版本号即代表安装成功。
