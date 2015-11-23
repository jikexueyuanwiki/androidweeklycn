# Issue #178
>
原文链接：<http://androidweekly.net/issues/issue-178>

> [点击订阅邮箱](http://tinyletter.com/androidweeklycn)第一时间掌握 Android Weekly 中文版更新动态

## 条款 & 教程

**[在 Android 开发中加载本地库存在一定的危险](https://medium.com/keepsafe-engineering/the-perils-of-loading-native-libraries-on-android-befa49dce2db)**
（medium.com）  
关于如何解决 Android 本地库存在的问题，本文应该是一个不错的向导。自从 Android PackageManager 中的本地库加载变得不可靠以后，相关人员又创建了一个更加健壮的解决办法，他们称它为“ReLinker”，请在“库和代码”部分点击进行查看。

**[性能列表控件](http://willowtreeapps.com/blog/performance-listviews/)**
（willowtreeapps.com）  
本文讲述了关于如何编写优化的列表控件适配器代码的一个快速提示。

**[AppBar - 第 2 部分](https://blog.stylingandroid.com/appbar-part-2/)**
（blog.stylingandroid.com）  
在本系列文章的第 2 部分中，马克·阿利森将我们的注意力转向了 ViewPager 组件，即当用户在 ViewPager 组件中进行滑动翻页时改变外部折叠工具条中的特征图像。

**[在 Android 应用中调试 RxJava](http://fernandocejas.com/2015/11/05/debugging-rxjava-on-android/)**
（fernandocejas.com）  
在本文中，费尔南多-塞哈斯向您介绍了他是如何调试 RxJava Observables 和 Subscribers的。

**[在单元测试和 instrumentation 测试中共享代码](http://blog.danlew.net/2015/11/02/sharing-code-between-unit-tests-and-instrumentation-tests-on-android/)**
（blog.danlew.net）  
在本文中，Dan Lew 就关于在单元测试和 instrumentation 测试的问题上给出了一些比较好的建议。

**[在活动中使用 Theme.NoDisplay](https://plus.google.com/105051985738280261832/posts/LjnRzJKWPGW)**
（plus.google.com）  
来自于 Android 平台团队的 Dianne Hackborn 说，如果我们使用 Theme.NoDisplay，那么我们就可以立即注销一个Activity。

**[Percent Support Library 库 23.1 版本中添加了长宽比设置](https://plus.google.com/+AndroidDevelopers/posts/ZQS29a5yroK)**
（plus.google.com）  
在 Percent Support Library 库 23.1 版本中，增加了一个功能，就是我们可以通过 app:layout_aspectRatio 属性去设置自定义的长宽比。

**[把 Plaid 转换为  Kotlin：经验和总结](http://antonioleiva.com/plaid-kotlin-1/)**
（antonioleiva.com）  
安东尼奥·莱瓦将最近发布的 Plaid 演示应用程序的首页视图转换成为了 Kotlin，并且他想要把生成的代码进行比较，来弄清楚这其中有什么东西得到了提升。

**[干净的架构之 jibber-jabber 到底是什么？ - 第 2 部分](http://pguardiola.com/blog/clean-architecture-part-2/)**
（pguardiola.com）  
在本文中，Pablo Guardiola 提到了一些分层架构，解释了一些它们的共同点以及它们各自的优点和缺点。除了 jibber-jabber，他还介绍了一下 Hexagonal 架构以及它的一些潜在的弊端。

**[build.gradle 无处不在](http://www.genymobile.com/the-build-gradle-ubiquity/)**
（www.genymobile.com）  
语法适用范围：允许您从文件中注入 Gradle 脚本。

**[动态 Android 依赖版本其实很好](https://brock.io/post/repeatable_android_builds/)**
（brock.io）  
最近我一直在阅读有关于为何动态的 Android 依赖版本的是弊大于利的博客。虽然具有确定性的建立项目是非常重要的，但是这并不意味着你应该放弃所有的动态版本。

**[利用冷启动时间来让您的应用程序看起来运行得更快](https://plus.google.com/105148560373589648355/posts/V3Tp6xxUWHH)**
（plus.google.com）  
在传统的运行过程中，窗口的预览和实际内容的加载是不同步的，如果使用冷启动，就能让你在视觉上认为它们是一致的，这样的话，您可能会产生一个幻觉，即认为您的应用程序运行得更快了

## 工作

**[Dropbox 招聘 Android 软件开发工程师](https://www.dropbox.com/jobs/listing/109128)**
（Seattle, WA）  
Dropbox 移动应用小组的重点是要在 Dropbox 应用程序上进行创新，不断的改善技术基础和用户界面。快来吧，和我们一起创造 Dropbox 的未来！

**[X-团队招募 Android 开发者](http://bit.ly/1OnVLcN)**
（远程）  
我们正在寻找对 Android 具有广泛理解的开发人员。当然，我们期望的比较完美的候选人应该对不同的库和框架高度熟练。我们需要 100 个远程开发者，如果你成为一个远程开发人员，那么我们将会提供一定的资金来帮助你实现你的目标。

**[Android 开发者](https://app.jobvite.com/j?cj=o7YR0fwG&s=Android_Weekly)**
（墨尔本，悉尼）  
如果你是一个很棒的 Android 开发者，并且你想要做一些比较出众的项目，那你就来加入我们的团队吧，我们是在一个具有协作精神和创造力的工作环境下工作，并且我们常常给自己时间去研究，创新以及开发那些可以引领行业的项目。

## 库和代码

**[Frodo](https://github.com/android10/frodo)**
（github）  
Frodo 是一个可以在日志输出框（logcat）中输出 RxJava Observables 和 Subscribers 日志的 Android 库。

**[ReLinker](https://github.com/KeepSafe/ReLinker)**
（github）  
 一个强大的 Android 本地库加载器。

**[自编译的 Android 应用程序](https://github.com/Tribler/self-compile-Android)**
（github）  
这是一个对应用程序能够进行自编译，进行突变以及传播病毒的强有力的证明。不过目前只能在 Android 4.3_r3.1 Galaxy Nexus 或者 android 5.1.1 下的 Cyanogenmod 12.1上进行运行，并且已经在  Nexus 5,6 和 10 版本的设备上得到了证实。

**[EmailAutoCompleteTextView](https://github.com/tasomaniac/EmailAutoCompleteTextView)**
（github）  
EmailAutoCompleteTextView 是一个带有内置邮件的适配器的 AutoCompleteTextView 组件，这个库可以自动的在你的控件清单中添加 GET_ACCOUNTS 权限。对于 Android 棉花糖而言，这个库还能处理运行时的权限。

**[KotlinFPWebinar ](https://github.com/mikehearn/KotlinFPWebinar)**
（github）  
这是Kotlin函数式编程研讨会的部分代码。

## 新闻

**[Android 平台分布的最新数字](http://www.androidpolice.com/2015/11/05/android-platform-distribution-numbers-updated-marshmallow-makes-its-first-appearance-at-0-3/)**
（www.androidpolice.com）  
谷歌更新了开发者所处平台的新的分布情况，这其中最值得关注的就是，现在棉花糖设备比冻酸奶更多了。

## 视频和播客

**[Droidcon 伦敦 2015 的视频可以看啦](https://skillsmatter.com/conferences/6712-droidcon-2015#skillscasts)**
（skillsmatter.com）  
现在大部分来自 Droidcon 伦敦 2015 的视频可以观看了。

**[Google Play Services 8.3 添加了新功能](https://www.youtube.com/watch?v=nAUeEJ51Cko)**
（www.youtube.com）  
Google Play services 8.3 现在刚刚发布出来，它可以让你建立一些更好的应用程序，可以增加一些比如：登录，连接位置提供程序，应用程序邀请以及可穿戴的数据层 API 等新功能。

**[Design Notes 之 Kirill Grouchnikov ](http://www.designnotes.fm/all/with-kirill-grouchnikov)**
（www.designnotes.fm）  
在本周，Liam Spradlin 和谷歌的 UI 工程师 Kirill Grouchnikov 进行了交流，介绍了一些比较显著的像素。

**[在 Ktolin 中进行函数式编程](https://www.youtube.com/watch?v=AhA-Q7MOre0)**
（www.youtube.com）  
Mike Hearn 通过一些实际的例子对如何在 Ktolin 中进行函数式编程进行了简要的介绍，即在您编码的时候，创建一个带有自动完成提示的文本字段。

**[Android开发者后台：Webview](http://androidbackstage.blogspot.com/2015/11/episode-37-webview.html)**
（androidbackstage.blogspot.com）  
Chet 和来自 WebView 团队的 Ben Murdoch 和 Richard Coles 进行了会谈。

**[和 Hadi Hariri 谈一谈 Kotlin](http://fragmentedpodcast.com/episodes/20/)**
（fragmentedpodcast.com）  
Fragmented 团队的业务最佳人选 —— Hadi Hariri 和我们谈论了有关于什么东西可以在 Kotlin Android 开发中称为新的希望？ 

**[这些 toggle 可以为我做什么？](https://www.youtube.com/watch?v=Fmf9b2EqqIE&feature=youtu.be)**
（www.youtube.com）  
Andrea Falcone 研究了一些 Android 系统开发者选项中的一些更复杂的选项，并且他谈到了一个开发人员应该使用它们来调试应用程序中出现的问题。

**[距离信标 - GDG Denver](https://www.youtube.com/watch?v=J7zYSa5wb6w&feature=youtu.be)**
（www.youtube.com）  
Dave Smith 介绍了谷歌进行信标管理的距离信标 API 以及用于客户端发现附近邮件的 API。
