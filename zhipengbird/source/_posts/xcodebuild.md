---
title: xcodebuild 和 altool 工具详解
date: 2017-05-10 17:23:06
categories: xcode_archive
tags:
- xcodebuild
- altool
---

## xcodebuild 是什么？  
 xcodebuild 是一款用来打包 Xcode projects 或者 workspaces 的命令行工具

> Tips: 在终端输入  `man xcodebuild` 可以查看具体用法，也可以去查看[官方文档](https://pewpewthespells.com/blog/migrating_code_signing.html)

-----------------------

1. 查看xcodebuild的简洁用法 `xcodebuild -usage`
```sh
Usage: 
       xcodebuild [-project <projectname>] [[-target <targetname>]...|-alltargets] [-configuration <configurationname>] [-arch <architecture>]... [-sdk [<sdkname>|<sdkpath>]] [-showBuildSettings] [<buildsetting>=<value>]... [<buildaction>]...

       xcodebuild [-project <projectname>] -scheme <schemeName> [-destination <destinationspecifier>]... [-configuration <configurationname>] [-arch <architecture>]... [-sdk [<sdkname>|<sdkpath>]] [-showBuildSettings] [<buildsetting>=<value>]... [<buildaction>]...

       xcodebuild -workspace <workspacename> -scheme <schemeName> [-destination <destinationspecifier>]... [-configuration <configurationname>] [-arch <architecture>]... [-sdk [<sdkname>|<sdkpath>]] [-showBuildSettings] [<buildsetting>=<value>]... [<buildaction>]...

       xcodebuild -version [-sdk [<sdkfullpath>|<sdkname>] [<infoitem>] ]
       xcodebuild -list [[-project <projectname>]|[-workspace <workspacename>]] [-json]
       xcodebuild -showsdks
       xcodebuild -exportArchive -archivePath <xcarchivepath> -exportPath <destinationpath> -exportOptionsPlist <plistpath>
       xcodebuild -exportLocalizations -localizationPath <path> -project <projectname> [-exportLanguage <targetlanguage>...]
       xcodebuild -importLocalizations -localizationPath <path> -project <projectname>
```

参数详解：
`-project projectname`

指定工程名，当同一目录下有多个工程时必须指定。

`-target tagrgetname`

指定编译的target的名称。

`-alltargets`

编译工程中的所有target。

`-worksace workspacename`

指定 workspace 的名称。

`-scheme schemename`

指定 scheme 的名称，编译 workspace 时是必须的。

`-destination destinationspecifier`

使用 destinationspecifier 指定的设备作为目标设备。默认与 scheme 中选择的兼容。

`-destination-timeout timeout`

设置搜索目标设备的超时时间，默认是30秒。

`-configuration configurationname`

当编译每个 target 时使用 configurationname 指定的配置。

`-arch architecture`

当编译每个 target 时使用 architecture 指定的架构类型。

`-sdk [<sdkfullpath> | <sdkname>]`

指定编译时所用的 SDK。参数可以是 SDK 的绝对路径，也可以是 SDK 的名称。

`-showsdks`

列出所有 Xcode 可识别的可用的 SDK，这个参数不会启动编译。

`-list`

列出工程中的所有 target 和 配置，或者 workspace 的 schemes。不会启动编译。

`-derivedDataPath path`

覆盖编译 workspace 的 scheme 时的结果数据存放的路径。

`-resultBundlePath path`

编译 workspace 的 scheme 时把一个 bunndle 写到指定的路径。

`-exportArchive`

指定一个可以被导出的 archive 文件。需要 -exportFormat，-archivePath和-exportPath` 配合使用，不能在编译时单独使用。

`-exportFormat format`

指定需要被导出的 archive 文件的格式。可行的格式是 IPA（iOS 包文件），PKG（Mac 包文件）和 APP。如果未指定，则 xcodebuild 则会自动检测使用IPA 或 PKG 格式。

`-archivePath xcarchivepath`

指定 archive 路径。

`-exportPath destinationpath`

指定导出的目标文件路径。

`-exportProvisioningProfile profilename`

指定导出 archive 文件时所使用的 provisioning pofile。

`-exportSigningIdentity identityname`

指定导出 archive 文件时所使用的应用签名 id。在可能的情况下，这个可以被 -exportProvisioningProfile 自动推导出来。

`-exportInstallerIdentity identityname`

指定导出 archive 文件时所使用的安装签名 id。如果可能，这个可以被 -exportSigningIdentity 或 -exportProvisioningProfile 自动推导出来。

`-exportWithOriginalSigningIdentity`

指定创建可被导出的 archive 文件时所使用的签名文件。

`buildaction ...`

指定一个或多个编译 target 时的编译行为。可行的编译行为有： - build 编译根环境下（SYMROOT）的 target。默认行为。

1. analyze 编译并且分析根环境（SYMROOT）下的一个 target 或者 scheme。需要指定一个 scheme。

2. archive Archive 根环境（SYMROOT）下的一个 scheme。需要指定一个 scheme。

3. test 测试跟环境（SYMROOT）下的一个 scheme。需要指定一个scheme。需要指定一个 scheme 和一个可选的目标。

4. installsrc 拷贝工程代码到源代码根目录（SRCROOT）。

5. install 编译 target 并且安装到发布目录下的 target 安装目录。

6. clean 从编译根目录下（SYMROOT）移除编译产品和中间文件。

`-xcconfig filename`

当编译所有 targets 的时候从指定的文件加载编译设置。这些设置会覆盖其他的编译设置，包括命令行中单独传递的设置。

`-dry-run, -n`

打印本来要执行但是未执行的命令。

`-skipUnavailableActions`

跳过不能执行的编译而不是失败。这个选项仅仅在 -scheme 参数使用了的时候有效。

`setting=value`

设置编译设置的值为 value。

`-userdefault=value`

设置用户默认设置为 value。

`-version`

显示 Xcode 的版本信息。不会启动编译。当和 -sdk 一起使用的时候，将会显示指定的 SDK 的版本信息，或者 -sdk 后面未指定 SDK 则会显示所有的 SDK 版本信息。另外，如果指定了 infoitem 参数则一个单行的版本信息将会显示。

`-usage`

显示 xcodebuild 的使用信息。
------------------------
1. 编译工程文件
`xcodebuild -project ActionSheet.xcodeproj  -target ActionSheet -configuration Release build`

2. 编译工作空间文件
`xcodebuild -workspace Emoticon.xcworkspace -scheme Emoticon build`

总结用法
1. `xcodebuild [-project name.xcodeproj] [[-target targetname] ... | -alltargets] build`
    build 指定 project，其中 -target 和 -configuration 参数可以使用 xcodebuild -list 获得，-sdk 参数可由 xcodebuild -showsdks 获得，[buildsetting=value ...] 用来覆盖工程中已有的配置。 action... 的可用选项如下, 打包的话当然用 build，这也是默认选项

2. `xcodebuild -workspace name.xcworkspace -scheme schemename build`
    build 指定 workspace，当我们使用 CocoaPods 来管理第三方库时，会生成 xcworkspace 文件，这样就会用到这种打包方式.


> 常用的build action有如下：
   > * build
    Build the target in the build root (SYMROOT). This is the default action, and is used if no action is given.

   > * analyze
    Build and analyze a target or scheme from the build root (SYMROOT). This requires specifying a scheme.

   > * archive
    Archive a scheme from the build root (SYMROOT). This requires specifying a scheme.

   > * test
    Test a scheme from the build root (SYMROOT). This requires specifying a scheme and optionally a destination.

   > * installsrc
    Copy the source of the project to the source root (SRCROOT).

   > * install
    Build the target and install it into the target’s installation directory in the distribution root (DSTROOT).

   > * clean
    Remove build products and intermediate files from the build root (SYMROOT).


3. 编译并生成.xcarchive包xcodebuild archive [-optionName]...

```
xcodebuild archive -archivePath /Users/UserName/Desktop/App/archive/XXX -workspace XXX.xcworkspace -scheme XXX -configuration Debug -sdk iphoneos9.3
```

```
命令              	                说明
-archivePath PATH	                保存生成.xcarchive包路径
-workspace NAME	                    指定工作空间文件XXX.xcworkspace
-scheme NAME	                    指定构建工程名称
-configuration [Debug/Release]  	选择Debug或者Release构建
-sdk NAME	                        指定编译时使用的SDK
```
4. .archive包导出ipa文件xcodebuild -exportArchive [-optionName]...

```
xcodebuild -exportArchive -archivePath /Users/UserName/Desktop/App/archive/XXX.xcarchive -exportPath /Users/UserName/Desktop/App/ipa/ -exportOptionsPlist /Users/UserName/Desktop/App/XXX.plist
```

```
命令              	    说明
-archivePath        	选择要导出的.xcarchive包路径
-exportPath	            导出ipa保存目录
-exportOptionsPlist	    导出过程中需要的配置文件路径
```

5. Export Options Plist 打包plist文件method 选项
method: (String) The method of distribution, which can be set as any of the following:

* app-store
* enterprise
* ad-hoc
* development
* teamID: (String) The development program team identifier.
* uploadSymbols: (Boolean) Option to include symbols in the generated ipa file.
* uploadBitcode: (Boolean) Option to include Bitcode.
生成plist文件中需要包含作上述的 method键值对  其他三个可选

ps: plist文件格式如下，字段可根据需要增加：(小编就因格式不对，折腾了好久)
  ```
  <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
        <key>teamID</key>
        <string>****</string>
        <key>method</key>
        <string>app-store</string>
        <key>uploadSymbols</key>
        <true/>
</dict>
</plist>
  ```

6. 获取工程的target列表 `xcodebuild -list -json` 得以一个json字符串
```
{
  "project" : {
    "targets" : [
      "Mask",
      "MaskTests",
      "MaskUITests",
      "MaskRandomUITests"
    ],
    "schemes" : [
      "Mask"
    ],
    "configurations" : [
      "Debug",
      "Release"
    ],
    "name" : "Mask"
  }
}%
```

#  altool 上传 ipa 
您可用 altool：Application Loader 的命令行工具来验证并上传您的应用程序二进制文件到 App Store。

若要在上传或自动上传有效的构建版本到 App Store 之前验证您的构建版本，可将 altool 包含进您的持续集成系统中。altool 在 `Application\ Loader.app/Contents/Frameworks/ITunesSoftwareService.framework/Versions/A/Support/` 文件夹中。

若要运行 altool，请在命令行执行以下一项操作：
```sh 
 altool --validate-app -f file -u username [-p password] [--output-format xml]
 altool --upload-app -f file -u username [-p password] [--output-format xml]
```
参数详解
其中                指定
`--validate-app`      您要验证的应用程序。
`--upload-app`        您要上传的应用程序。
`-f file`             您正在验证或上传的应用程序的路径和文件名。
`-u username`         您的用户名。
`-p password`         您的用户密码。
`--output-format [xml | normal]`
您要 Application Loader 以结构化的 XML 格式还是非结构化的文本格式返回输出信息。Application Loader 默认以文本格式返回输出信息。
[Application Loader](http://help.apple.com/itc/apploader/#/apdATD1E53-D1E1A1303-D1E53A1126)




另附上小编编写一个便捷的打包工具 [xcodearchive](https://github.com/zhipengbird/xcode_archive),欢迎各位star^_^





