# 延期的共享元素转换(3b) 

这篇文章我们通过讨论 Lollipop Transition API：延期的共享元素转换，来继续深入分析共享元素转换。这是这一系列文章的第四部分，我会通过以下观点展开： 

- 第一部分：[以 Activity & Fragment Transitions 开始](http://www.androiddesignpatterns.com/2014/12/activity-fragment-transitions-in-android-lollipop-part1.html)
- 第二部分：[深入内容转换](http://www.androiddesignpatterns.com/2014/12/activity-fragment-content-transitions-in-depth-part2.html) 
- 第三部分 a：[深入共享元素转换](http://www.androiddesignpatterns.com/2015/01/activity-fragment-shared-element-transitions-in-depth-part3a.html) 
- 第三部分 b：[延期共享元素转换](http://www.androiddesignpatterns.com/2015/03/activity-postponed-shared-element-transitions-part3b.html)
- 第三部分 c：实现共享元素回调(很快出现！)
- 第四部分：Activity & Fragment Transitions 示例(很快出现！)

首先我们讨论由于常见问题导致推迟共享元素转换的需要。 

## 理解问题 

当处理共享元素转换时，一个常见的问题源于这样的事实：它们很早就出现在 Activity 生命周期的框架中。回顾[第 1 部分](http://www.androiddesignpatterns.com/2014/12/activity-fragment-transitions-in-android-lollipop-part1.html)，`转换`必须捕获目标视图的开始和结束状态，来构建一个正常运转的动画。因此，如果框架在它的共享元素给出最后的大小，位置以及被调用的 Activity 内的大小之前，开始共享元素转换，那么转换会为它的共享元素捕获错误的结束值，由此产生的动画将完全错误(错误的输入转换示例，见 **视频 3.3**)。 

在转换开始之前，共享元素的结束值是否被计算出来主要取决于两个因素：(1)被调用的 activity 的布局的复杂性和深度，(2)调用的 activity 加载所需数据的时间。布局越复杂，那么决定共享元素在屏幕上显示的位置和大小所花费的时间就越长。同样地，如果 activity 内共享元素的最终外观取决于异步加载数据，那么在数据传递回主线程之前，框架有可能自动的启动共享元素转换。下面列出的是一些你可能会遇到的常见的问题： 

- **共享元素在一个`Fragment`中，它寄存在被调用的 activity 中。**  
- [FragmentTransactions 在被命令后不是立即执行](https://developer.android.com/reference/android/app/FragmentTransaction.html#commit())；当主线程的工作结束后，它们稍后执行。所以，如果共享元素在 `Fragment` 的视图层的内部，并且 `FragmentTransaction` 执行的不够迅速，那么框架有可能在共享元素正确衡量之前就启动共享元素转换，并在屏幕上布局。[1](http://www.androiddesignpatterns.com/2015/03/activity-postponed-shared-element-transitions-part3b.html#footnote1)

- **共享元素是一个高分辨率的图像**。设置一个超过 `ImageView` 最初的范围的高分辨率图像，可能会[引发额外的布局](https://github.com/android/platform_frameworks_base/blob/lollipop-release/core/java/android/widget/ImageView.java#L453-L455)甚至视图层次,因此在共享元素准备好之前就开始转换的可能性会增加。流行的位图加载/扩展库的异步性质，如 [Volley](https://android.googlesource.com/platform/frameworks/volley) 和  [Picasso](http://square.github.io/picasso/)，不会可靠的解决这个问题：框架没有图像被下载，扩展，和/或从后台线程的磁盘获取的这些先验知识，所以无论图像是否正在被处理，框架都会启动共享元素转换。 

- 共享元素依赖与异步加载数据。 
如果在共享元素最终出现在被调用的 activity 之前，需要从 `AsyncTask`, `AsyncQueryHandler`, `Loader` 或类似的东西上下载数据，框架可能会在数据传回主线程之前启动转换。 

## `postponeEnterTransition()` 和 `startPostponedEnterTransition() `

在这一点上你可能会想,“*要是有办法暂时延迟转换,直到我们确定共享元素已正确测量和布局。*” 嗯,你很幸运,因为Activity Transitions API[2](http://www.androiddesignpatterns.com/2015/03/activity-postponed-shared-element-transitions-part3b.html#footnote2)给了我们一个这样做的方法! 

想要暂时性的阻止共享元素转换启动，在你调用的 activity 的 `onCreate()` 方法中调用 [postponeEnterTransition()](https://developer.android.com/reference/android/app/Activity.html#postponeEnterTransition())。之后，当你确定共享元素已经正确的定位和定形后，调用 [startPostponedEnterTransition()](https://developer.android.com/reference/android/app/Activity.html#startPostponedEnterTransition()) 来恢复转换。你会发现一个有用的常见的模式，在 [OnPreDrawListener](https://developer.android.com/reference/android/app/Activity.html#startPostponedEnterTransition()) 中启动延期转换，当共享元素被测量并布局后会被调用：[3](http://www.androiddesignpatterns.com/2015/03/activity-postponed-shared-element-transitions-part3b.html#footnote3)

```
    @Override
	protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    // Postpone the shared element enter transition.
    postponeEnterTransition();

    // TODO: Call the "scheduleStartPostponedTransition()" method
    // below when you know for certain that the shared element is
    // ready for the transition to begin.
	}

	/**
 	* Schedules the shared element transition to be started immediately
 	* after the shared element has been measured and laid out within the
 	* activity's view hierarchy. Some common places where it might make
 	* sense to call this method are:
 	* 
 	* (1) Inside a Fragment's onCreateView() method (if the shared element
 	*     lives inside a Fragment hosted by the called Activity).
 	*
 	* (2) Inside a Picasso Callback object (if you need to wait for Picasso to
 	*     asynchronously load/scale a bitmap before the transition can begin).
 	*
 	* (3) Inside a LoaderCallback's onLoadFinished() method (if the shared
 	*     element depends on data queried by a Loader).
 	*/
	private void scheduleStartPostponedTransition(final View sharedElement) {
    sharedElement.getViewTreeObserver().addOnPreDrawListener(
        new ViewTreeObserver.OnPreDrawListener() {
            @Override
            public boolean onPreDraw() {
                sharedElement.getViewTreeObserver().removeOnPreDrawListener(this);
                startPostponedEnterTransition();
                return true;
            }
        });
    }
```

忽略名字，这两种方法也可以用来延迟共享元素返回转换。简单的推迟返回转换，调用 activity 的 [onActivityReenter()](https://developer.android.com/reference/android/app/Activity.html#onActivityReenter(int,%20android.content.Intent)) 方法：[4](http://www.androiddesignpatterns.com/2015/03/activity-postponed-shared-element-transitions-part3b.html#footnote4)

```
    /**
 	* Don't forget to call setResult(Activity.RESULT_OK) in the returning
 	* activity or else this method won't be called!
 	*/
	@Override
	public void onActivityReenter(int resultCode, Intent data) {
    super.onActivityReenter(resultCode, data);

    // Postpone the shared element return transition.
    postponeEnterTransition();

    // TODO: Call the "scheduleStartPostponedTransition()" method
    // above when you know for certain that the shared element is
    // ready for the transition to begin.
    }
```

尽管共享元素转换更流利可靠，但是将延期共享元素添加到你的应用程序中有可能会引起潜在地副作用，注意到这点也很重要： 

- 在调用 `postponeEnterTransition` **之后****不要忘记调用** `startPostponedEnterTransition()`：忘记调用会使你的应用程序处于死锁的状态，阻止用户进入下一个 Activity 屏幕。 
- 不要推迟转换超过几分之一秒。推迟甚至几分之一秒的转换可能会给应用程序中引入不必要的延迟，恼人的用户，以此减慢了用户的体验。 

一如往常，感谢阅读！如果你有任何问题，请随意留下评论，如果你觉得文章对你有帮助，不要忘记+1和/或分享这篇文章。 

1 当然，大多数应用程序可以通过调用 [FragmentManager#executePendingTransactions()](https://developer.android.com/reference/android/app/FragmentManager.html#executePendingTransactions()) 来解决这个问题，该方法会强迫待定的 `FragmentTransactions` 立即执行而不是异步执行。 

2 注意 `postponeEnterTransition()` 和 `startPostponedEnterTransition()` 方法只位Activity Transitions 工作，不为 Fragment Transitions 工作。关于解释及可能的解决方案，见 [this StackOverflow answer](http://stackoverflow.com/q/26977303/844882) 和 [this Google+ post](https://plus.google.com/+AlexLockwood/posts/3DxHT42rmmY).  

3 专家提示：在调用 [View#isLayoutRequested()](http://developer.android.com/reference/android/view/View.html#isLayoutRequested()) 之前，是否需要分配 `OnPreDrawListener`，如果是必须的，[View#isLaidOut()](http://developer.android.com/reference/android/view/View.html#isLaidOut()) 在某些情况下可能会派上用场。 

4 一个测试你的共享元素返回/重新输入转换的行为的好方法是通过进入 Developer Options 和启用"Don't keep activities"设置。这将有助于测试最坏的情况,即在返回转换开始之前，调用的 activity 需要重建其布局,重新查询必要的数据等。
