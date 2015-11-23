# 在Android Lollipop上使用JobScheduler API

在本教程中，您将学习如何使用 `JobScheduler` API适用于 Android Lollipop。当满足一定的条件时，该`JobScheduler ` API允许开发者创建后台执行的工作。

## 介绍

当使用Android进行工作时，会遇到这样的情况——你会想在将来的某个时间或在一定条件下运行任务，例如当一个设备接入电源或连接到Wi-Fi网络。值得庆幸的是有API21，因为Android Lollipop而被大多数人所知，谷歌已经提供了被称为`JobScheduler` API的新组件来处理这样的情况。

当一组预定义的条件得到满足时，`JobScheduler` API的应用程序执行一项操作。不像 `AlarmManager` 类，该时间测定时不准确的。此外，该 `JobScheduler` API 能够一同批处理各种工作。这允许应用程序执行特定的任务，同时考虑设备的电池在定时控制上的成本。

在这篇文章中，通过使用`JobScheduler` API和 `jobservice` 类在一个Android应用程序中运行一个简单的后台任务，您将学习更多关于  `JobScheduler` API和 jobservice 类方面的知识。在本教程中的代码可以在 [GitHub](https://github.com/tutsplus/Android-JobSchedulerAPI)上获得。

### 1、创建工作服务

开始，你会想使用最小所需的API 21创建一个新的Android项目，因为在Android的最新版本中已经增加了`JobScheduler` API ，在写作的时候， 它没有通过支持库向后兼容。

假设你使用的是Android Studio，在你点击完新项目按钮后，你会看到一个基本的“Hello World”应用。你要进行这个项目的第一步是创建一个新的Java类。为了让事情简单化，命名它为`JobSchedulerService` ，并扩展`jobservice` 类，这就需要创建两个方法：`onStartJob(JobParameters params)` 和`onStopJob(JobParameters params)`。

``` 
    public class JobSchedulerService extends JobService {
    @Override
    public boolean onStartJob(JobParameters params) {
 
        return false;
    }
 
    @Override
    public boolean onStopJob(JobParameters params) {
         
        return false;
    }
 
    }
```

当你开始你的任务时，`onstartjob（jobparameters params）` 是你必须使用的方法，因为它是系统用来触发已经安排的工作的。正如你可以看到，该方法返回一个布尔值。如果返回值是`false`，该系统假定任何任务运行不需要很长时间并且到方法返回时已经完成。如果返回值是`true`，那么系统假设任务是需要一些时间并且负担落到你（开发者）的身上，当给定的任务完成时通过调用`jobFinished(JobParameters params, boolean needsRescheduled)`告知系统。

当收到取消请求时，`onStopJob(JobParameters params)` 是系统用来取消挂起的任务的。重要的是要注意到，如果`onStartJob(JobParameters params)`返回 `false`，当取消请求被接收时，该系统假定没有目前运行的工作。换句话说，它根本就不调用`onStopJob(JobParameters params)`。

有一点要注意的是，工作服务在你的应用程序的主线程上运行。这意味着，你必须使用另一个线程处理程序，或运行时间更长的任务异步任务以不阻塞主线程。由于多线程技术超出本教程的范围，让我们保持简单和实现处理程序来运行在`JobSchedulerService`类中的任务。

```     
    private Handler mJobHandler = new Handler( new Handler.Callback() {
    @Override
    public boolean handleMessage( Message msg ) {
        Toast.makeText( getApplicationContext(), 
            "JobService task running", Toast.LENGTH_SHORT )
            .show();
        jobFinished( (JobParameters) msg.obj, false );
        return true;
    }
    } );
```

在处理程序中，你执行`handleMessage(Message msg)`方法（`Handler`实例的一部分），并使用它运行你的任务的逻辑。在此情况下，让事情简单化，从应用程序发送一个`Toast`信息，虽然这是在那里你可以像同步数据一样给出你的逻辑。

当任务完成时，你需要调用`jobFinished(JobParameters params, boolean needsRescheduled)`让系统知道你完成了那项任务，它可以开始排队接下来的操作。如果你不这样做，你的工作将只运行一次，你的应用程序将不被允许执行额外的工作。

在`onStartJob(JobParameters params)`方法中，`jobFinished(JobParameters params, boolean needsRescheduled)` 的两个参数值是JobParameters传递到JobService类，一个布尔值让系统知道是否需要根据工作的最初要求重新编排工作。这个布尔值对于理解是非常有用的，因为它帮助你了解如何处理由于其他问题（如一个失败的网络电话）而导致你的任务无法完成的情况。

随着Handler程序实例的创建，你可以继续并开始执行`onStartJob(JobParameters params)`和`onStopJob(JobParameters params)`方法来控制你的任务。你会发现，在下面的代码片段中，`onStartJob(JobParameters params)`方法的返回值为true。这是因为，你要使用一个Handler 程序实例来控制你的操作，这意味着相比`onStartJob(JobParameters params)`方法它可能需要更长的时间来完成。通过返回`true`，你让应用程序知道，你将手动调用`jobFinished(JobParameters params, boolean needsRescheduled)`方法。你也会注意到，数字1被传递到Handler示例。这是相关引用工作中你将用到的标识符。

```
    @Override
    public boolean onStartJob(JobParameters params) {
    mJobHandler.sendMessage( Message.obtain( mJobHandler, 1, params ) );
    return true;
    }
 
    @Override
    public boolean onStopJob(JobParameters params) {
    mJobHandler.removeMessages( 1 );
    return false;
    }
```

一旦你使用Java部分的`JobSchedulerService`类完成，你需要研究AndroidManifest.xml，并增加服务节点，因此，你的应用程序有权限绑定和使用`JobService`类。

```
<service android:name=".JobSchedulerService"
    android:permission="android.permission.BIND_JOB_SERVICE" />
```

## 2、创建工作计划

随着`JobSchedulerService` 类完成，我们可以开始考虑你的应用程序将如何与`JobScheduler API`相互作用。你需要做的第一件事就是创建一个`JobScheduler`对象，在示例代码中调用`mJobScheduler` ，通过获得一个系统服务的实例`JOB_SCHEDULER_SERVICE`来初始化它。在示例应用程序中，这是在`MainActivity` 类中完成的。

```
mJobScheduler = (JobScheduler) 
    getSystemService( Context.JOB_SCHEDULER_SERVICE );
```

当你想创建你的计划任务时，你可以使用来`JobInfo.Builder`创建一个`JobInfo`对象，字符指针传递到你的服务中。为了创建一个`JobInfo`对象，`JobInfo.Builder` 接受两个参数。第一个参数是你将运行工作的标识符，第二个参数是你将与`JobScheduler API`.一同使用的服务的组件名称。

```
JobInfo.Builder builder = new JobInfo.Builder( 1,
        new ComponentName( getPackageName(), 
            JobSchedulerService.class.getName() ) );
```

当你的工作将执行时，这个编辑器可以让你设置许多不同的控制选项。下面的代码片段显示了你如何设置你的任务每三秒定期运行一次。

```
builder.setPeriodic( 3000 );
```

其他方法包括：

- `setMinimumLatency(long minLatencyMillis)`：这会使你的工作不启动直到规定的毫秒数已经过去了。这是与setPeriodic(long time)不兼容的，并且如果同时使用这两个函数将会导致抛出异常。

- `setOverrideDeadline(long maxExecutionDelayMillis)`：这将设置你的工作期限。即使是无法满足其他要求，你的任务将约在规定的时间已经过去时开始执行。类似于`setMinimumLatency(long time)`，这个函数是与 `setPeriodic(long time)` 互相排斥的，并且如果同时使用这两个函数，将会导致抛出异常。

`setPersisted(boolean isPersisted)`：这个函数告诉系统，在设备重新启动后，你的任务是否应该继续存在。

- `setRequiredNetworkType(int networkType)`：这个函数会告诉你工作，只有在设备处于一种特定的网络中时，它才启动。它的默认值是`JobInfo.NETWORK_TYPE_NONE`，这就意味着，无论是否有网络连接，该任务均可以运行。另外两个可用的类型是`JobInfo.NETWORK_TYPE_ANY`，这需要某种类型的网络连接可用，工作才可以运行；以及`JobInfo.NETWORK_TYPE_UNMETERED`，这就要求设备在非蜂窝网络中。

- `setRequiresCharging(boolean requiresCharging)`：使用这个函数会告诉你的应用程序，除非设备开始充电，否则工作不会启动。

- `setRequiresDeviceIdle(boolean requiresDeviceIdle)`：这会告知你的工作不会启动，除非用户不使用他们的设备，并且他们已经有一段时间没有使用它。

重要的是要注意到，`setRequiredNetworkType(int networkType)`、`setRequiresCharging(boolean requireCharging)`和`setRequiresDeviceIdle(boolean requireIdle)`可能会导致你的工作永远不启动，除非`setOverrideDeadline(long time)`还设置允许即使不符合条件的情况下，你的工作也可以运行。一旦规定优先条件，你可以创建JobInfo对象，并发送它到你的`JobScheduler`对象，如下所示。

```
if( mJobScheduler.schedule( builder.build() ) <= 0 ) {
    //If something goes wrong
}
```

你会注意到，`schedule`操作返回一个整数。如果`schedule` 失败，它将返回一个为零或更少的值，对应于一个错误代码。否则，它将返回在`JobInfo.Builder`中定义的作业标识符。

如果你的应用程序需要你停止特定或所有工作，你可以通过对`JobScheduler` 对象调用`cancel(int jobId)`或`cancelAll()`实现。

```
mJobScheduler.cancelAll();
```

你现在应该能够使用JobScheduler API以及你自己的应用程序进行批处理工作和运行后台操作。

## 结论

在这篇文章中，你已经学会了如何实现一个子类，使用一个Handler对象来运行你的应用程序的后台任务。你也学会了如何使用JobInfo.Builder来设置你的服务应该运行时的要求。通过这些，你应该能够在留意功率消耗的同时，提高你自己的应用程序操作。

