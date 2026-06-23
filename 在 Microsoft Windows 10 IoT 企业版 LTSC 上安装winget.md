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

