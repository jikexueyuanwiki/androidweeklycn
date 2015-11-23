# Issue #167

>
原文链接：<http://androidweekly.net/issues/issue-167>

> [点击订阅邮箱](http://tinyletter.com/androidweeklycn)第一时间掌握 Android Weekly 中文版更新动态

## 条款 & 教程

**[最近的 SDK，API 文档，GitHub 上的案例来啦](https://plus.google.com/+AndroidDevelopers/posts/NPagF6bpHsv)**
（plus.google.com）  
使用 Nearby.Messages 来给你的安卓和 iOS 应用增加跨平台代理。它可以在100步之内(或者使用 "earshot" 将它限制在5步之内)工作，即使用户的蓝牙没起作用也没有关系。没有新的 manifest 权限并且可以始终向后移植到 Gingerbread。

**[这应该成为一个库吗?](http://ryanharter.com/blog/2015/08/20/should-this-be-a-library/)**
（ryanharter.com）  
Ryan Harter 来自于咨询世界，在这里有一个共同的特性，就是让所有的事物都可以成为一个库。这里有几点建议可以帮助您决定是否让一个事物成为一个库。

**[JR Raphael 认为 M 代表 Marshmallow](http://theblerg.net/podcast/11)**
（theblerg.net）  
Chris Lacy 和客人 JR Raphael 讨论了本周的所有信息。他们打破了 M 的官方名称，也讨论了该名称对未来的 Android 版本号和 logo 的潜在影响，当然也讨论了 M 更新的预览版本。

**[调色画板](http://andraskindler.com/blog/2015/tinting_drawables/)**
（andraskindler.com）  
这篇短文章讲述了如何给画板和位图着色来匹配当前主题。

**[百分比是基于 Percent 支持库的尺寸和边距](https://plus.google.com/+AndroidDevelopers/posts/C8oaLunpEEj)**
（plus.google.com）  
谷歌推出了新的 Percent 支持库。其中的 PercentFrameLayout 和 PercentRelativeLayout 提供了简单的方法来根据总体大小的百分比来指定视图尺寸和边距。

**[关于 Android  6.0 SDK 的一些思考](https://commonsware.com/blog/2015/08/17/random-musings-android-6p0-sdk.html)**
（commonsware.com）  
每当谷歌发布一个新的 SDK 平台，Mark Murphy 都会翻阅各种资料信息，看看是否有一些开发人员值得注意的事情。

**[通过谷歌的信标平台获得附件](http://googledevelopers.blogspot.com/2015/08/whats-in-message-getting-attachments.html?linkId=16440270)**
（googledevelopers.blogspot.com）  
在谷歌信标平台中，你可以通过使用 Proximity Beacon API 中的附件来将信息和已经注册了的信标进行结合，然后通过使用 Nearby Messages API 向用户的设备以消息的形式提供这些附件。

**[关于 Preventative Optimizations 的真相](https://medium.com/google-developers/the-truth-about-preventative-optimizations-ccebadfd3eb5)**
（medium.com）  
Colt McAnlis 解释了为什么它不是"premature"而是"preventative"。

**[为 Android Studio 创建插件](http://yvonne.idescout.com/2015/08/writing-plugins-for-android-studio.html)**
（yvonne.idescout.com）  
为 Android Studio 创造一个插件和为 IntelliJ IDEA 创造一个插件差不多，只是多了一些额外的步骤。

**[TextInputLayout](https://blog.stylingandroid.com/textinputlayout/)**
（blog.stylingandroid.com）  
新的 TextInputLayout 为标准 EditText 控件添加了一些比较好的补充来提供改进的提示和错误文本处理。在这篇文章中，Mark Allison 将会告诉你如何使用它，并且告诉你如何避免一些关于错误文本的疑难杂症。

**[即使是一个小的漏洞也会使一艘大船沉没](https://corner.squareup.com/2015/08/a-small-leak.html)**
（corner.squareup.com）  
当 Pierre Yves Ricau 在构建 LeakCanary 的时候，这篇文章还是一封内部的电子邮件。他发现了奇怪的内存泄漏，并且开始去弄清楚发生了什么事。

**[不要打断用户流](http://hannesdorfmann.com/android/dont-interrupt-user-flow/)**
（hannesdorfmann.com）  
在这篇深入的博客中 Hannes Dorfmann 演示如何使用 RecyclerView 生成一个不打断用户流的用户体验。

## 赞助

**[精美简单的应用程序内部消息](https://supportkit.io/?utm_source=androidweekly)**
（supportkit.io）  
将 SupportKit 添加到您的应用程序中，更赞的是，用户可以像给他们朋友发消息那样轻松的给您发消息。用户写消息的界面十分漂亮 — — 他们无需离开您的应用程序 — — 就能把消息发送到您的收件箱，Slack，或者您最喜欢的 CRM。您的答复会立即被传递回应用程序。

**[使用 Intel® XDK  简化您的工作流程 ](https://software.intel.com/en-us/intel-xdk?cid=&utm_content=General_Developers&utm_medium=Newsletter%20Placement&utm_source=Android%20Weekly&utm_campaign=Android%20ASMO%20Q3%2015%20Digital%20Marketing%20Campaign)**
（software.intel.com）  
一次编写，到处发布。简单易用的 Intel® XDK 提供了一个简单的开发工作流来帮助您让您的 HTML5 应用程序可以更快的发布。通过许多形成因素和应用程序商店来设计、 调试和部署 HTML5 应用程序。详情请点击这里。

## 设计

**[即使把名字换成 Splash screen 还是很糟 ](http://www.androiduipatterns.com/2015/08/splash-screen-with-any-other-name-is.html)**
（www.androiduipatterns.com）  
因为某种深不可测的原因，谷歌决定将 Splash Screens 添加到他们的设计准则中。虽然他们现在称之为"Launch screen"，但是还是同样的东西，即在加载应用程序时显示给用户的界面。

## 工作

**[高级的 Android 工程师-帮助我们建立一个 SDK!
](https://pspdfkit.com/jobs/#section_android)**
(远程)   
我们正在为成千上万的应用程序提供引擎，比如在汉莎航空的驾驶舱以及伦敦地铁中使用的 Dropbox。PSPDFKit 将会是目前为止您做过的最有趣和最复杂的项目之一。我们有一个优秀的团队，并且它正在壮大。

**[高级的 Android 开发者](https://www.grandcentrix.net/2015/08/16/news/jobs/senior-android-entwickler-mw-koeln/)**
(科隆，德国，多特蒙德)   
快来加强我们的团队吧，我们正在寻找热情 Android 开发人员到我们设在科隆的总部和多特蒙德的新地址来开发本机应用程序。我们提供一个令人兴奋的设备和舒适的工作环境。

**[https://pspdfkit.com/jobs/#section_android](https://pspdfkit.com/jobs/#section_android)**
（远程）  
我们正在使用 RxJava & c + + 14 为安卓系统建立 PSPDFKit 。它是一个具有挑战性的项目，并且它被许多应用程序使用，这里还有很多工作要做。我们有一个有才华的团队，他们享受远程环境中工作的好处。如果你想进行挑战，请将您的简历发给我们。

**[高级 Android 开发者](http://rolp.co/9Liy8)**
（旧金山）  
Constant Contact 公司正在寻求一个高级的 Android 开发者来帮助我们建立 SMB Android 应用程序的下一个层面。你将与我们的 Android 和 iOS 移动开发团队密切合作，建设一些我们集成在使用 RxJava、 Dagger 等框架建立的应用程序中核心技术。

## 库与代码

**[Android-Boilerplate](https://github.com/hitherejoe/Android-Boilerplate)**
（github.com）  
是一个演示如何下载，持久化存储，数据同步的简单的样本应用程序，它在应用程序之间使用一个常见的布局来进行展示。它描述了设计支持库、 数据缓存和新的测试模块的使用特征，并且将功能测试与生产代码进行了分离。

**[CoordinatorLayoutExample](https://github.com/saulmm/CoordinatorLayoutExample)**
（github.com）  
本示例试图复制 Hangouts 4.0 的 profile 动画来演示如何使用 CoordinatorLayout.Behavior。

## 新闻

**[支持库 API 的变化的报告 ](https://developer.android.com/sdk/support_api_diff/23/changes.html)**
（developer.android.com）  
本报告详细介绍了 Android 支持库 API 在 22.2.1 到 23 的变化。

**[在 Support v23 库中已修复的问题](https://code.google.com/p/android/issues/list?can=1&q=label:Version-Support-23)**
（code.google.com）  
这里是最新的 v23 版本的支持库中已修复的问题列表。

## 视频

**[Genymotion 2.5 Android 测试和持续集成 ](https://www.youtube.com/watch?v=atrjbbLtEHE)**
（www.youtube.com）  
一个现场的研讨会使用新的 Genymotion 2.5 展示 Android 检测测试和持续集成。

**[Android 对话框: Katherine Kuan ](https://www.youtube.com/watch?v=8ZQrdSOvnJE&feature=youtu.be)**
（www.youtube.com）  
Chiu-Ki 与 谷歌开发者的主持者 Katherine Kuan 闲逛的时候谈到了关于 Android 的教学问题。

## 事件

**[应用程序世界将于 11 月 18-19 日在伦敦举办](http://europe.apps-world.net/?utm_source=mediapartner&utm_medium=banner&utm_campaign=Andriod%20weekly)**
（europe.apps-world.net）  
该活动汇集了开发商，领先的品牌、 电信、 初创企业和投资者，并且仍然提供了唯一的平台以满足，合作伙伴，以及整个全球应用社区!拥有 11 个会议，300 + 令人敬畏的发言者，250 多家参展商，启动区，以及 Appsters 奖项；这个事件看起来将会形成一个相互交流的兴奋的繁荣景象。
