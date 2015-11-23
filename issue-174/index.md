# Issue #174

>
原文链接：<http://androidweekly.net/issues/issue-174>

> [点击订阅邮箱](http://tinyletter.com/androidweeklycn)第一时间掌握 Android Weekly 中文版更新动态

## 条款 & 教程

**[函数响应式编程的介绍](https://realm.io/news/droidcon-gomez-functional-reactive-programming/)**
（realm.io）  
在 Droidcon NYC 2015 的演讲中，Juan Gomez 解释了你为什么应该使用 FRP，主要涉及三个话题：reactive extensions 编程、评论和说明，以及围绕 RxJava 展开的中间主题。

**[在 Android 上尝试 Kotlin 的10（11）种理由](https://plus.google.com/+OmarMiatello/posts/jBCuqFJ8gnx)**
（plus.google.com）  
在 Android 上尝试 Kotlin 的10（11）种理由。我想这大概也会是 11 种理由。

**[在安卓系统上分享带有运行权限模型的文件](http://blog.danlew.net/2015/10/07/sharing-files-on-android-in-a-world-with-runtime-permissions/)**
（blog.danlew.net）  
当我们需要升级到运行权限模型的时候，我们会遇到越来越多的问题，目前为止最让 Dan Lew 头疼的就是分享文件。

**[运行权限、文件 以及 ACTION_SEND](https://commonsware.com/blog/2015/10/07/runtime-permissions-files-action-send.html)**
（commonsware.com）  
在过去的几年中，当开发者需要去移动 content:// Uri 值而不是 file:// Uri 值的时候，特别是譬如 ACTION_SEND 等事物，会感到特别有压力。然而 Android 6.0 的运行权限制度进一步加深了这种压力。

**[为什么要使用 Observable.create()  方法，而不是仅仅从 Observable 直接继承？](http://www.grokkingandroid.com/why-use-observable-create-and-not-just-inherit-from-observable/)**
（www.grokkingandroid.com）  
当你开始去使用 RxJava 的时候，你需要去创建 Observable，因为他们是 RxJava 的核心，但是我们应该如何去做呢？

**[数据绑定–第 5 部分](https://blog.stylingandroid.com/data-binding-part-5/)**
（blog.stylingandroid.com）  
到目前为止 Mark Allison 介绍了一些非常有用的数据绑定技术，但是这里仍然还有一些可以让它更强大的功能，比如：Observable。

**[将 Retrofit 从1.9 升级到版本 2 的指南](https://futurestud.io/blog/retrofit-2-upgrade-guide-from-1-9/)**
（futurestud.io）  
本指南将帮助你把你的 Retrofit 应用程序升级到下一个版本。当您升级到版本 2 的时候，你会发现这里面有很多重大的更改。

**[Android 棉花糖中当前的一些解决方案](https://futurestud.io/blog/retrofit-2-upgrade-guide-from-1-9/)**
（futurestud.io）  
请注意！目前在 Android 棉花糖中解决类似“Implicit Intent”问题的方式和以前已经不一样了。如果您还是像以前一样，这样有可能会终止您的应用程序的运行。

**[延迟加载 Dex 文件](https://medium.com/@Macarse/lazy-loading-dex-files-d41f6f37df0e)**
（futurestud.io）  
Carlos Sessa 在这里分享了一些他的一些关于 MulyiDex 的技巧，以及它如何让我们去减少启动时间的一些方法。

**[Android 谷歌地图入门](http://code.tutsplus.com/tutorials/getting-started-with-google-maps-for-android-intermediate--cms-24739)**
（code.tutsplus.com）  
在本教程中，你将会学到比您以前在文章看到的那些技术的一个更大的扩展。本文介绍了关于在地图顶部放置视图菜单，覆盖室内层的选择控制器以及在您的应用程序中添加一个街道视图的组件的相关内容。

**[Kotlin：一个在 Java 6 废墟上的新希望](https://realm.io/news/droidcon-michael-pardo-kotlin/)**
（code.tutsplus.com）  
在 Droidcon NYC 2015 中的这个演讲中，Michael Pardo 介绍了 Kotlin，它是一种被 JetBrains 支持的静态类型的 JVM 语言，它带有譬如匿名函数，类扩展以及 null-safty 等功能。它的目的是让应用程序更简练，更加具有表达性和互操作性。

**[Espresso：保存以及还原状态](http://blog.sqisland.com/2015/10/espresso-save-and-restore-state.html)**
（blog.sqisland.com）  
您是否保存或者还原您的活动，碎片以及自定义视图的状态？您会测试它们吗？一个测试保存和还原状态的方法是在您的 Espresso 测试中进行旋转屏幕操作。

**[改进 Android 的方法限制 - 第 2 部分](https://developers.soundcloud.com/blog/congratulations-you-have-a-lot-of-code-remedying-androids-method-limit-part-2)**
（developers.soundcloud.com）  
在这一部分中，Matthias Käppler  分享了一个用来处理使用 dex 方法计数问题的替代选项：使用多个DEX文件。

## 赞助

**[使用 PacketZoom 的 SDK 来进行更快的加载内容](https://www.packetzoom.com/?ref=androidweekly)**
（www.packetzoom.com）  
PacketZoom 提供了一个专门为本地移动应用程序而设计的基于协议的技术服务。它可以在用户在不同的网络之间移动的时候继续进行无间隙的下载。我们专注于速度，而你便可以专注于内容。您的应用程序需要一个 PacketZoom 的SDK。今天，您可以在我们的网站上免费下载。

## 设计

**[停止不必要的用户界面动画 ](https://medium.com/@sophie_paxtonUX/stop-gratuitous-ui-animation-9ece9aa9eb97)**
（medium.com）  
在这篇短文章中，我们将探讨关于在 UI 设计中过渡使用动画的问题，将和早期的一些视觉设计进行对比并且给出一些关于 GUI 运动设计的比较有效的建议。

**[您的 UI 并不是迪斯尼电影](https://medium.com/@sophie_paxtonUX/your-ui-isn-t-a-disney-movie-703f7fbd24d2)**
（medium.com）  
比较成熟的 UI 设计师的一个最明显的特点之一就是具有克制力。即能知道您选择的平台的性能，并且不会使用它。（直到它变得完美）

## 工作

**[Android开发者(新西兰、芝加哥、波士顿)](http://www.onfarminnovations.com/)**
（medium.com）  
OFI 正在寻求 1 个更有经验的 Android 开发者来帮我解决“小项目”。我们将在Q1/16 推出一个农业产业的突破性方案。你需要每周花费大概12小时，并且持续 2-3 年的时间，那么您将得到股权和利润分享。如果合格的话，将会共享财务细节。

**[Android工程师 - 创建一个SDK](https://pspdfkit.com/jobs/#android)**
（任何地点）  
您想要和一个使用尖端技术并且充满动机和人才的队伍一同工作吗？你是否在使用 Java 的时候但是也不害怕接触 C++？你是否喜欢反应性编程？ 你是否喜欢设计 API 以及创建数百万人都在使用的东西？建造的东西？如果你喜欢，那和我们一起工作吧！

## 库和代码

**[Android 截图测试](http://facebook.github.io/screenshot-tests-for-android/)**
（facebook.github.io）  
自动化测试有助于帮助防止在 Android 应用程序中出现视觉回归！

**[Dexcount--gradle插件](https://github.com/KeepSafe/dexcount-gradle-plugin)**
（github.com）  
这是一个 Gradle 插件用来报告在您的每个工程中的 APK 中有多少个引用的方法。

**[RxPermissions](https://github.com/tbruyelle/RxPermissions)**
（github.com）  
这个库允许 RxJava 与新的 Android M 权限模型一同使用。

## 工具

**[Crystal CLI](https://crystal.sh/products/cli)**
（crystal.sh）  
对任何的软件，语言、框架、库、应用程序、平台、运行库、服务器、数据存储和文件都可以自动生成代码。

## 业务

**[让您的应用程序在发布后持续保持受欢迎的状态](http://www.smashingmagazine.com/2015/10/keep-your-android-app-popular-after-launch/)**
（www.smashingmagazine.com）  
当您发布了您的应用程序以后，它很受欢迎。那么，你该如何保持这一势头，并确保您的程序会越来越受欢迎？

## 视频和播客

**[精简Android应用：消除代码开销](https://www.youtube.com/watch?v=b6zKBZcg5fk&feature=youtu.be)**
（www.youtube.com）  
Jake Wharton 的演讲把重点放在了技术上，他阐述了库以及应用程序都是可以实现的，以保证它们的效果一般情况下没有间接开销。

**[为 Android 开发人员准备的 Effective Java：第三节](http://fragmentedpodcast.com/episodes/18/)**
（fragmentedpodcast.com）  
在本节中，团队成员将向您介绍第三项要点：使用私有构造函数或者枚举类型来执行单列模式属性。
