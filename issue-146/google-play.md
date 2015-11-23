# 针对Jenkins的谷歌商店安卓出版插件

## 优点

- 上传APK文件到Google Play
	
  - 这包括使用多个APK支持的应用程序
	
- 上传APK扩展（.obb）文件
	
  - 从现有的应用程序中选择与使用扩展文件，例如补丁发布
	
- 安排应用程序的alpha、beta或产品发布
	
  - 这包括将现有的应用程序移动到不同轨道的构建步骤
	
  - 例如，你可以在一份工作上传一个alpha，然后后来又另一个工作推动它到beta
	
- 生产应用程序的分级部署
	
- 为各种语言分发发布说明到上传的文件
	
- 如果由于某些原因配置不好，或上传应用程序失败，改变构建结果
	
- 每一个配置段都支持变量扩展，允许动态生成的发布说明
	
- 结合[Google OAuth Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Google+OAuth+Plugin)，所以凭证可以一次性地进入全局和工作之间的共享
	
 - 很多Google Play账号也是通过这一机制支持

## 计划即将到来的特点

- 为应用程序上传截图和文本
	
## 需要

### Jenkins
 
[版本1.565.1](http://jenkins-ci.org/changelog-stable#v1.565.1)或以上。

### Google Play发布账号

仅为了初始设置，你必须能够登入拥有[Google Play](https://developer.android.com/distribute/googleplay/start.html)发布账号的谷歌帐户。

注意：仅有管理员权限是不够的；你必须是帐户的所有者。

你可以在Google Play开发者控制台是通过 [Settings → User accounts & rights](https://accounts.google.com/ServiceLogin?service=androiddeveloper&passive=1209600&continue=https://play.google.com/apps/publish/&followup=https://play.google.com/apps/publish/#AdminPlace)查看帐户的所有者是谁。

### 请注意

- 任何上传的APK将由Google Play立即发布；它们不会处在遴选或挂起状态

- 上传的应用程序必须已经在Google Play中存在；你不能使用API上传新的应用程序
	
- `尽管你只是上传alpha 或 beta版本的应用程序，你仍然需要为Jenkins提供 Manage Production APKs 许可`
	
 - Google Play API团队具有“指出这种型号（2014年8月）和仍在研究最安全和最有效的方式以解决使用API接入问题”（自2015年4月开始）

## 启动

### 以前的：设立Google Play凭据

下面的这个视频显示了初始安装过程： [https://www.youtube.com/watch?v=txdPSJF94RM](https://www.youtube.com/watch?v=txdPSJF94RM&list=PLhF0STyfNdUk1R3taEmgFR30yzp41yuRK&index=1)

#### 安装插件

通过Jenkins插件管理器安装这个插件。

如果进行手动安装，确保 [Google OAuth Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Google+OAuth+Plugin)及其依赖项已经安装。

#### 创建谷歌服务帐户

为了使自动访问你的Google Play帐户，你必须创建一个服务帐户：

1、作为所有者登录到Google Play开发者控制台
 
2、选择设置→API访问

3、点击“创建新的项目”

4、建立新项目后，点击“创建服务帐户”

5、链接到谷歌开发者控制台

6、创建一个新的OAuth客户端ID

7、选择服务帐户类型

8、要注意到一个.p12文件已经下载，可能会命名为“Google Play Android Developer-xxxxxxxxxxxx.p12”

9、复制创建服务帐户的电子邮件地址


#### 服务帐户权限分配

1、返回Google Play开发者控制台页面

2、在对话框上点击 “完成”

3、请注意，服务帐户与Google Play发行帐户关联

4、单击帐户的“授予访问权限”按钮

5、至少确保下列权限启用：

- 编辑存储列表、定价和分销

- 管理制作的APK

- 管理Alpha和Beta APK

- 管理Alpha 和Beta用户

6、点击“添加用户”

7、现在你可以登录Google Play发行帐户了

#### 添加服务账号到Jenkins：

1、导航到你的Jenkins实例

2、从Jenkins的侧边栏选择“证书”

3、选择一个证书域并点击“添加证书”

4、从“类”下拉列表，选择“谷歌服务帐户密钥”

5、输入证书名称——实际值是不重要的

6、选择“P12密钥”类型

7、上传由谷歌开发者控制台下载的.p12文件

8、点击“OK”创建证书

为了将应用程序发布到Google Play，Jenkins需要证书和权限。

### Per-job config

#### 上传一个APK

以下设置过程在这个视频中演示：[https://www.youtube.com/watch?v=iu-bLY9-jkc](https://www.youtube.com/watch?v=iu-bLY9-jkc&list=PLhF0STyfNdUk1R3taEmgFR30yzp41yuRK&index=2)

1、创建一个新的自由形式的软件项目

2、通过你的需求建立步骤，确保你想上传的这个APK在建立的工作空间是可用的

3、编译后添加“上传的Android APK到Google Play”

4、从下拉列表中选择证书名称

- 证书必须属于拥有应用程序上传权限的Google Play账户
- 
5、输入路径和/或通配符指向上传的APK或多个APK

- 这可能是一个Ant式的*/-release.apk，或者一个相对于工作空间根的逗号分隔的文件名列表
	
6、选择跟踪到应当配置的APK

- 如果你使用Jenkins配置作品的发布，你可以选择推出的百分比
	
7、选择“添加语言”到将发布的说明以及上传的APK

- 你可以根据需要添加或多或少的你支持的语言，但是每一种语言都必须已经添加到你的应用程序中，位于Google Play开发者控制台的“商店列表”下。
	
#### APK扩展文件

你可以选择为上传的每个APK增加两个[扩展文件](https://developer.android.com/google/play/expansion-files.html)。

扩展文件列表可以以与的应用程序相同的方式指定，但注意，它们必须以[main|patch].<expansion-version>.<package-name>.obb.格式命名。你可以从在线帮助获取更多细节。

#### 移动现有的APK到另一个版本的轨道

如果你已经上传一个应用程序到alpha轨道（例如），你可以在以后使用Jenkins重新分配该版本为beta版或产品发布轨道。

根据工作配置的“构建”章节，增加了“移动AndroidAPK到不同发布轨道”的设置步骤，和配置新的发布轨道。

你可以通过告诉Jenkins 哪一个APK需要移动或直接输入APK版本代码，或通过提供APK文件，该插件会为你读取申请ID和版本代码。

### 想要的反馈

如果你是扩展文件的用户，特别是如果你使用多个APK情况下，这将有助于了解你如何正常地使用扩展文件，且无论什么样的插件都提供了足够的信息。
你通常会为每个应用程序的扩展做独立的主/补丁文件吗？或者它是更常见的所有APK共享相同的扩展文件吗？

目前来说，当试图从现有的应用程序中重新利用扩展文件时（没有明确地指定那些来自于旧APK的扩展文件需要与新上传的APK一同重新使用），前者的情况下是不合适的。

通过在页面顶部的电子邮件告诉我们！

### 疑难解答

来自于插件的错误信息（其中许多都直接来自于Google Play API）应当一目了然。

如果你获得特定配置工作中遇到了麻烦，请尝试手动上传相同的APK到Google Play。这样你可能就会看到失败的原因，例如版本代码冲突或类似。

或者，请给我们发送错误报告或包含有相信信息的邮件，包括构建控制台日志输出；在页面顶部的信息框内查看我们的联系信息。

### 常见问题

#### 如果我想上传多个、不同应用ID的APK程序（即构建风格），该怎么办？

使用Android Gradle构建系统的构建风格特点，它可能是有一个单一Android构建产生多个APK，每一个APK有一个不同的应用ID。

例如，你可以有应用程序ID“com.example.app”和“com.example.app.pro”分别为免费和付费版本。

这些往往是建立在一个单一的Jenkins的工作中，有人想知道为什么这个插件将拒绝上传的在一个单一的工作中具有不同应用ID的APK。

然而，就Google Play所考虑的方面而言，这些都是完全独立的应用程序。这是正确的，因此应该在单独的Jenkins构建（每一个应用ID）上传。

如果您想尝试在一个Jenkins构建中上传完全不同的APK（比如三个），这就需要用Google Play API开放和提交三个独立的“编辑会话”。如果其中任何一个失败——也许是因为一个无效的APK，或由于一个API失败（不幸的是，使用Google Play API过程中这种情况并不少见）——你将使你的Google Play帐户处于不一致的状态而结束。您的构建将被标记为失败，但有一个或多个应用程序实际上已经上传并发布到Google Play，所以你就必须手动修复这种情况。此外，你将不能仅进行重新运行构建，因为这将由于已经存在的APK而失败。

在这种情况下的最佳方法是有一项工作构建不同的风格（即APK使用不同的应用程序ID），然后，如果构建成功，这将存档的APK和启动多个“下游”Jenkins构建，这将单独发布的每个应用程序。

即“上传”的工作可能是通用的，可以通过参数接收APK的信息。

待办事项： 提供更多关于如何设置的信息。












