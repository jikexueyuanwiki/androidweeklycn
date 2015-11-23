# 仅作为Android 调试模式工具的Stetho

最近Facebook发布了一名为`Stetho`的工具，这个工具可以使我们通过Chrome Developer工具来检查Android 应用程序。我发现这非常有用，因为这个工具还可以访问应用程序中的SQLite数据库。 很明显，这种类型的工具应包含于Android 应用程序的调试模式中。这里有一个很好的方法来完成这个工作。

## 添加依赖

为了确保Stetho仅用于调试模式，你可添加一个`debugCompile`（调试编译）的依赖，而不是常常使用到的`compile`（编译）类型。

```
depencencies {
// your other dependencies here...
    debugCompile 'com.facebook.stetho:stetho:1.0.0'
}
```

## 在调试模式中初始化Stetho

现在我们需要在调试模式中使用Stetho。如何做呢？ 我们可以使用具有强大功能的Android Gradle构建系统！ 借此添加仅在调试模式中编译的一些源文件，创建一个名为`src/debug/java`的源文件夹。这个文件夹和`src/main/java` 相似，但它是用来存放应用程序中的调试变量的。相反，主文件夹存放所有变量共用的源文件。 之后，按照`Stetho `主页上描述的方式添加一个Application应用。

```
import com.facebook.stetho.Stetho;

public class MyDebugApplication extends MyApplication {
    @Override
    public void onCreate() {
        super.onCreate();
        Stetho.initialize(
                Stetho.newInitializerBuilder(this)
                        .enableDumpapp(Stetho.defaultDumperPluginsProvider(this))
                        .enableWebKitInspector(Stetho.defaultInspectorModulesProvider(this))
                        .build());
    }
}
```

看清楚这个类是如何从已经有的 `MyApplication`.类进行扩展的。这种方法确实很方便，因为你已经在应用程序中使用一个应用进行其他类型的初始化了。如果你还没有一个应用（application）可从`android.app.Application`.继承一个就行了。

## 激活调试应用

最后一步是确保当前应用程序的调试版本使用的是 `MyDebugApplication`类。此外，在这里我们用Gradle 搭建系统来实现这个步骤。那就是在`AndroidManifest.xml` 文件添加至`src/debug` 文件夹中。

```
<manifest
    package="com.mycompany"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        tools:replace="android:name"
        android:name=".MyDebugApplication"/>

</manifest>
```

这个`AndroidManifest.xml`文件将并入到`src/main` 文件夹中的主`AndroidManifest.xml`文件里，并且会替换<application> 标签中的`android:name`属性，其原因是我们特别使用了`tools:replace`属性。真是太棒了！

现在如果我们运行应用程序的调试模式变量，`Stetho `就将激活。如果我们转为发布变量，此变量将无迹可寻且Stetho也不会激活。发布版本没有出现偶然故障，程序开发人员的工作完成的很好。

## 结论

使用Android Gradle构建系统可以很容易在应用程序中添加一些调试功能。这个技术不仅可以用在`Stetho`上，还可以用在那些仅仅希望在调试模式中添加的类库或者工具的工作中。
