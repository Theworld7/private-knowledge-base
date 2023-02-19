# 常用操作

## 卸载 Microsoft Store

右键开始 > 选择 Windows PowerShell (Admin) > 窗口中输入以下命令：
`get-appxpackage *store* | remove-Appxpackage`

卸载了之后，重启电脑，之后再次安装：
右键开始 > 选择 Windows PowerShell (Admin) > 窗口中输入以下命令：
`add-appxpackage -register "C:\Program Files\WindowsApps\*Store*\AppxManifest.xml" -disabledevelopmentmode`

检查看看以下设置有没有勾选：
按 Win + R > 输入 inetcpl.cpl > 回车 > 高级 > 在最下面勾选 SSL 3.0 + TLS 1.0 + TLS 1.1 +TLS 1.2 > 应用后重启电脑。

另外，您可用 “Windows 10 覆盖安装” 来修复，可参考以下步骤：

下载地址：（点击 "立即下载工具“）
https://www.microsoft.com/zh-cn/software-download/windows10

如何进行Windows 10 覆盖安装：
https://answers.microsoft.com/zh-hans/windows/forum/all/%e5%a6%82%e4%bd%95%e8%bf%9b%e8%a1%8cwindows-10/a727e16a-f31a-48d4-8333-049eac965b49

获取最新 Windows 更新：
https://support.microsoft.com/zh-cn/windows/%E8%8E%B7%E5%8F%96%E6%9C%80%E6%96%B0-windows-%E6%9B%B4%E6%96%B0-7d20e88c-0568-483a-37bc-c3885390d212#WindowsVersion=Windows_10

## 解决Mac应用无法正常打开

### 关闭所有校验

```shell
sudo spctl --master-disable
```

### 关闭指定应用校验

```shell
sudo xattr -rd com.apple.quarantine /Applications/xxxxxx.app
```

## ADB

安装：`brew install android-platform-tools`

测试：`adb devices`

查看安装位置：`brew info android-sdk`

## Homebrew

安装：`/bin/zsh -c "$(curl -fsSL <https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh>)"`