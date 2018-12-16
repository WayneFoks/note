# Android四大组件划重点

## Service

### 重点知识

#### 调用方式

* startService
* bindService

#### bindService的两种用法

使用方法选型：

* Local
  * Binder类
* IPC
  * Message：单线程
  * AIDL：多线程

#### [生命周期](https://developer.android.com/guide/components/services?hl=zh-cn)

> 当服务与所有客户端之间的绑定全部取消时，Android 系统便会销毁服务（除非还使用 [`onStartCommand()`](https://developer.android.com/reference/android/app/Service.html?hl=zh-cn#onStartCommand%28android.content.Intent,%20int,%20int%29) 启动了该服务）。因此，如果您的服务是纯粹的绑定服务，则无需对其生命周期进行管理 — Android 系统会根据它是否绑定到任何客户端代您管理。
>
> 不过，如果您选择实现 [`onStartCommand()`](https://developer.android.com/reference/android/app/Service.html?hl=zh-cn#onStartCommand%28android.content.Intent,%20int,%20int%29) 回调方法，则您必须显式停止服务，因为系统现在已将服务视为_已启动_。在此情况下，服务将一直运行到其通过 [`stopSelf()`](https://developer.android.com/reference/android/app/Service.html?hl=zh-cn#stopSelf%28%29) 自行停止，或其他组件调用 [`stopService()`](https://developer.android.com/reference/android/content/Context.html?hl=zh-cn#stopService%28android.content.Intent%29) 为止，无论其是否绑定到任何客户端。

#### AIDL

> 只有允许不同应用的客户端用 IPC 方式访问服务，并且想要在服务中处理多线程时，才有必要使用 AIDL

**线程安全要求：**

> * 来自本地进程的调用在发起调用的同一线程内执行。如果该线程是您的主 UI 线程，则该线程继续在 AIDL 接口中执行。 如果该线程是其他线程，则其便是在服务中执行您的代码的线程。 （我理解是对UI线程做了特殊的处理）
> * 来自远程进程的调用分派自平台在您的自有进程内部维护的线程池。 您必须为来自未知线程的多次并发传入调用做好准备。 换言之，AIDL 接口的实现必须是完全线程安全实现。

### 参考文献

{% embed url="https://developer.android.com/guide/components/services?hl=zh-cn" %}

## Broadcasts

### 重点知识

#### Effects on process state

> Once your code returns from `onReceive()`, the BroadcastReceiver is no longer active. The system considers its process to be a low-priority process and may kill it to make resources available for other more important processes.
>
> For this reason, you should not start long running background threads from a broadcast receiver. After `onReceive()`, the system can kill the process at any time to reclaim memory, and in doing so, it terminates the spawned thread running in the process. To avoid this, you should either call [`goAsync()`](https://developer.android.com/reference/android/content/BroadcastReceiver.html?hl=zh-cn#goAsync%28%29)\(if you want a little more time to process the broadcast in a background thread\) or schedule a [`JobService`](https://developer.android.com/reference/android/app/job/JobService.html?hl=zh-cn) from the receiver using the [`JobScheduler`](https://developer.android.com/reference/android/app/job/JobScheduler.html?hl=zh-cn), so the system knows that the process continues to perform active work.

```java
public class MyBroadcastReceiver extends BroadcastReceiver {
    private static final String TAG = "MyBroadcastReceiver";

    @Override
    public void onReceive(final Context context, final Intent intent) {
        final PendingResult pendingResult = goAsync();
        AsyncTask<String, Integer, String> asyncTask = new AsyncTask<String, Integer, String>() {
            @Override
            protected String doInBackground(String... params) {
                StringBuilder sb = new StringBuilder();
                // ...
                Log.d(TAG, log);
                // Must call finish() so the BroadcastReceiver can be recycled.
                pendingResult.finish();
                return data;
            }
        };
        asyncTask.execute();
    }
}
```

#### Three Ways To Send Broadcasts



### 参考文献

{% embed url="https://developer.android.com/guide/components/broadcasts?hl=zh-cn" %}



