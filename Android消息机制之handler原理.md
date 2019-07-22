---
title: Android消息机制之handler原理
date: 2019-07-18 21:17:51
tags: Android
---
# 前言  
在Android开发中因为UI更新的操作都是在主线程中执行，而网络请求等耗时操作都是在子线程中执行的，因此会遇到很多多线程切换的问题。我们的项目中使用的是RXJava + retrofit，RXJava的observeOn和subcribeOn都能很方便的切换线程。如下形式：  
`.observeOn(AndroidSchedulers.mainThread())`  
它本质上还是通过handler来实现的。虽然用起来很方便，但是这样对于原理的学习并不是很友好，因此特意学习了下handler原理相关知识。  
# 一、handler基础知识  
## 1、基本概念  
*  MainThread(主线程)：也就是UI线程，在程序启动时自动创建。  
*  工作线程：开发者自己开启的子线程，执行一些耗时操作。  
*  Handler(处理者)：线程间通信的媒介，一是添加消息到消息队列(Message Queue)，二是处理Looper(循环器)分发的消息。  
*  Message(消息):Handler处理的对象，存储需要的操作信息。  
*  Message Queue(消息队列):是一个队列，先进先出，存储Handler发送来的消息。  
*  Looper: Message Queue和Handler的通信媒介，是一个死循环，不断从Message Queue中取出消息给Handler处理。  
   
## 2、 对应关系  
*  一个线程(Thread)只能对应一个循环器(Looper)，可理解为一个线程维持一个消息队列(Message Queue)。  
*  一个线程(Thread)可对应多个处理者(Handler)，可理解为一个消息队列(Message Queue)中的消息可分发给多个处理者(Handler)处理。  
*  一个循环器(Looper)可对应多个处理者(Handler)。  
*  一个处理者(Handler)只能对应一个循环器(Looper)，如果对应多个循环器(Looper)，那处理消息会错乱。  
  
# 二、Handler基本用法  
## 1、子线程和主线程通信  

```
public class MainActivity extends AppCompatActivity {
    Button button;
    Handler handler;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        button = findViewById(R.id.handlerSendMsg);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {  
            //在主线程中创建一个Handler处理子线程发来的消息
                handler = new Handler() {  
                //重写handleMessage方法，这个方法的msg参数就是从子线程传递过来的消息
                    @Override
                    public void handleMessage(Message msg) {  
                    //函数体中就是消息处理的逻辑，可以通过msg.what的消息标识来分别处理不同的消息逻辑
                        Log.d("mainThread","mainThread handleMessage:  " + msg.obj);
                        Toast.makeText(MainActivity.this,"mainThread handleMessage" + msg.obj,Toast.LENGTH_LONG).show();
                    }
                };
                childThread();
            }
        });
    }

    private void childThread(){  
     //创建一个子线程
        Thread childThread = new Thread() {
            @Override
            public void run() {
            //在子线程中通过Handler的obtainMessage获取一个Message的实例
                Message message = handler.obtainMessage();  
                /** 
                设置Message的属性  
                Message.what：用来标识信息的int值，通过该值主线程能判断出来自不同地方的信息来源  
                Message.arg1/Message.arg2：Message初始定义的用来传递int类型值的两个变量  
                Message.obj：用来传递任何实例化对象
                */
                message.obj = "childThread message";
                //通过sendMessage将消息发送出去
                handler.sendMessage(message);
            }
        };
        childThread.start();
    }
}
```
## 2、两个子线程通信  

```  
public class MainActivity extends AppCompatActivity {
    Button button;
    Handler handler;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        button = findViewById(R.id.handlerSendMsg);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                handlerMessageInTwoThread();
            }
        });
    }
    private void handlerMessageInTwoThread() {
        Thread firstThread = new Thread() {
            @Override
            public void run() {
            //在子线程中不调用Looper.prepare()会crash报错，下面会给出分析
                //Looper.prepare();
                handler = new Handler() {
                    @Override
                    public void handleMessage(Message msg) {
                        Log.d("firstThread","firstThread handleMessage:  " + msg.obj);                      Toast.makeText(MainActivity.this,"firstThread handleMessage" + msg.obj,Toast.LENGTH_LONG).show();
                    }
                };
                //不调用Looper.loop()就收不到消息
                //Looper.loop();
            }
        };

        Thread secondThread = new Thread() {
            @Override
            public void run() {
                Message sendMessage = handler.obtainMessage();
                sendMessage.obj = "secondThread发送的消息";
                Log.d("secondThread","secondThread sendMessage: " + sendMessage.obj);
                handler.sendMessage(sendMessage);

            }
        };
        firstThread.setName("firstThread");
        firstThread.start();
        secondThread.setName("secondThread");
        secondThread.start();
    }
}
```
如果不打开上面的两行注释代码，和之前的一样，那么运行会报错，报错信息如下：  

```  
...... E/AndroidRuntime: FATAL EXCEPTION: firstThread
    Process: com.example.androidtest, PID: 23777
    java.lang.RuntimeException: Can't create handler inside thread that has not called Looper.prepare()
        at android.os.Handler.<init>(Handler.java:200)
        at android.os.Handler.<init>(Handler.java:114)
        at com.example.androidtest.MainActivity$3$1.<init>(MainActivity.java:62)
        at com.example.androidtest.MainActivity$3.run(MainActivity.java:62)
```
意思就是在firstThread中没有调用Looper.prepare()。所以，需要加上Looper.prepare()。不过也可以在firstThread中创建Handler的时候指定一个主线程的Looper。Handler的构造函数是支持带参的，其中一个构造函数如下：  

```  
/**
     * Use the provided {@link Looper} instead of the default one.
     *
     * @param looper The looper, must not be null.
     */
    public Handler(Looper looper) {
        this(looper, null, false);
    }
```
因此，我们在firstThread中创建Handler的时候可以这样：  

```  
Thread firstThread = new Thread() {
            @Override
            public void run() {
                //Looper.prepare();  
                //通过Looper.getMainLooper()获取主线程的Looper
                handler = new Handler(Looper.getMainLooper()) {
                    @Override
                    public void handleMessage(Message msg) {
                        Log.d("firstThread","firstThread handleMessage:  " + msg.obj);
                        Toast.makeText(MainActivity.this,"firstThread handleMessage" + msg.obj,Toast.LENGTH_LONG).show();
                    }
                };
                //Looper.loop();
            }
        };
```
这里就产生了2个问题，第一，为什么主线程中不调用Looper.prepare()可以实现子线程和主线程的通信；第二，Looper.prepare()具体干了什么，为什么不调用就不能在两个子线程中通信。下面的分析主要解决这两个问题。  
# 三、Handler消息机制分析  
## 1、主线程为什么不需要调用Looper.prepare()  
主线程不需要调用Looper.prepare()，那么说明Android程序在启动的时候主线程中就已经做了这部分工作。这里涉及到一个问题就是Android程序的真正入口，就是ActivityThread的main()方法。[ActivityThread源码分析](https://blog.csdn.net/xu_song/article/details/81983724)。查看ActivityThread的main()方法源码：  
  
```  
public static void main(String[] args) {
        Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "ActivityThreadMain");

        // CloseGuard defaults to true and can be quite spammy.  We
        // disable it here, but selectively enable it later (via
        // StrictMode) on debug builds, but using DropBox, not logs.
        CloseGuard.setEnabled(false);
        Environment.initForCurrentUser();

        // Set the reporter for event logging in libcore
        EventLogger.setReporter(new EventLoggingReporter());

        // Make sure TrustedCertificateStore looks in the right place for CA certificates
        final File configDir = Environment.getUserConfigDirectory(UserHandle.myUserId());
        TrustedCertificateStore.setDefaultUserDirectory(configDir);

        Process.setArgV0("<pre-initialized>");

        Looper.prepareMainLooper();

        // Find the value for {@link #PROC_START_SEQ_IDENT} if provided on the command line.
        // It will be in the format "seq=114"
        long startSeq = 0;
        if (args != null) {
            for (int i = args.length - 1; i >= 0; --i) {
                if (args[i] != null && args[i].startsWith(PROC_START_SEQ_IDENT)) {
                    startSeq = Long.parseLong(
                            args[i].substring(PROC_START_SEQ_IDENT.length()));
                }
            }
        }
        ActivityThread thread = new ActivityThread();
        thread.attach(false, startSeq);

        if (sMainThreadHandler == null) {
            sMainThreadHandler = thread.getHandler();
        }
        if (false) {
            Looper.myLooper().setMessageLogging(new
                    LogPrinter(Log.DEBUG, "ActivityThread"));
        }
        // End of event ActivityThreadMain.
        Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
        Looper.loop();
//可见主线程的Looper是不能退出的
        throw new RuntimeException("Main thread loop unexpectedly exited");
    }
```
这个函数具体的分析参考上面链接，我们只关注其中和Looper相关的，可以发现有2行：  

```  
Looper.prepareMainLooper();  
Looper.loop();
```
我们继续看Looper.prepareMainLooper()这个函数里面干了什么  

```  
/**
     * Initialize the current thread as a looper, marking it as an
     * application's main looper. The main looper for your application
     * is created by the Android environment, so you should never need
     * to call this function yourself.  See also: {@link #prepare()}
     */
    public static void prepareMainLooper() {
        prepare(false);
        synchronized (Looper.class) {
            if (sMainLooper != null) {
                throw new IllegalStateException("The main Looper has already been prepared.");
            }
            sMainLooper = myLooper();
        }
    }
```
通过注释发现就是指定主线程的looper，并且不需要自己调，系统已经做了这部分工作。这里调用的prepare(false)和我们在子线程中调用的Looper.prepare()最终调用的都是一个函数  

```    
//Looper.prepare()最终调用的是prepare(true);
    public static void prepare() {
        prepare(true);
    }

    private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));
    }
```
所以仅有区别就是主线程的不能退出，子线程的可以。  
## 2、Looper.prepare()做了什么  
不管是主线程还是子线程都必须调用Looper.prepare()，那这个函数具体做了什么呢？其实就一行代码sThreadLocal.set(new Looper(quitAllowed));      

```  
/**
     * Sets the current thread's copy of this thread-local variable
     * to the specified value.  Most subclasses will have no need to
     * override this method, relying solely on the {@link #initialValue}
     * method to set the values of thread-locals.
     *
     * @param value the value to be stored in the current thread's copy of
     *        this thread-local.
     */
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
```
这里的入参是一个Looper，**这个Looper就是当前线程中创建的Looper(new Looper(quitAllowed))存放在了一个ThreadLocal中**。其实在所有线程中创建的Looper都存放在了一个ThreadLocal中。然后在创建Handler的时候就将Handler与当前线程关联。**所以，Looper.prepare()就是将当前线程创建的Looper存放在ThreadLocal中**。  
下面我们看下是如何关联：  
例如：handler = new Handler(Looper.getMainLooper()) {...}就是将创建的handler与主线程的Looper关联。*我们是可以指定特定的looper，让handleMessage运行在我们想运行的线程中。*构造函数如下：  

```  
public Handler(Looper looper, Callback callback, boolean async) {
        mLooper = looper;
        mQueue = looper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }
```
另外一种handler = new Handler() {...},调用如下：  

```  
/**
     * Default constructor associates this handler with the {@link Looper} for the
     * current thread.
     *
     * If this thread does not have a looper, this handler won't be able to receive messages
     * so an exception is thrown.
     */  
     //从上面注释可见如果没有looper就会抛异常，所以需要在之前调用Looper.prepare()创建一个looper
    public Handler() {
        this(null, false);
    }   
     
    //this(null, false);构造函数源码如下  
    public Handler(Callback callback, boolean async) {
        if (FIND_POTENTIAL_LEAKS) {
            final Class<? extends Handler> klass = getClass();
            if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                    (klass.getModifiers() & Modifier.STATIC) == 0) {
                Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                    klass.getCanonicalName());
            }
        }
//获取和此线程关联的looper
        mLooper = Looper.myLooper();
        if (mLooper == null) {
            throw new RuntimeException(
                "Can't create handler inside thread " + Thread.currentThread()
                        + " that has not called Looper.prepare()");
        }
    //对应的消息队列
        mQueue = mLooper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }
```
所以在创建handler的时候是通过 mLooper = Looper.myLooper();获取到此线程关联的looper，我们看下 Looper.myLooper()这个函数。  

```  
/**
     * Return the Looper object associated with the current thread.  Returns
     * null if the calling thread is not associated with a Looper.
     */
    public static @Nullable Looper myLooper() {
        return sThreadLocal.get();
    }   
      
  //get函数的源码  
public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }
```
代码很简单，就是从ThreadLocal获取关联的looper。真正获取是在get方法里，首先获取当前线程，然后从ThreadLocalMap中取获取和当前线程关联的looper，也就是在当前线程中调用Looper.prepare()时候存储的looper。  
# 四、消息处理流程  
解决完之前的两个问题，接下来分析消息的处理流程，只有来消息发送、消息获取、消息处理的完整流程走完，才能实现不同线程间的通信。  
## 1、消息发送  
从handler.sendMessage(sendMessage);一直往里面跟发现最终调用的是MessageQueue.java类中的boolean enqueueMessage(Message msg, long when)函数。  

```  
boolean enqueueMessage(Message msg, long when) {  
//target就是创建的handler
        if (msg.target == null) {
            throw new IllegalArgumentException("Message must have a target.");
        }
        if (msg.isInUse()) {
            throw new IllegalStateException(msg + " This message is already in use.");
        }

        synchronized (this) {
            if (mQuitting) {
                IllegalStateException e = new IllegalStateException(
                        msg.target + " sending message to a Handler on a dead thread");
                Log.w(TAG, e.getMessage(), e);
                msg.recycle();
                return false;
            }

            msg.markInUse();
            msg.when = when;
            Message p = mMessages;
            boolean needWake;
            if (p == null || when == 0 || when < p.when) {
                // New head, wake up the event queue if blocked.
                msg.next = p;
                mMessages = msg;
                needWake = mBlocked;
            } else {
                // Inserted within the middle of the queue.  Usually we don't have to wake
                // up the event queue unless there is a barrier at the head of the queue
                // and the message is the earliest asynchronous message in the queue.
                needWake = mBlocked && p.target == null && msg.isAsynchronous();
                Message prev;
                for (;;) {
                    prev = p;
                    p = p.next;
                    if (p == null || when < p.when) {
                        break;
                    }
                    if (needWake && p.isAsynchronous()) {
                        needWake = false;
                    }
                }
                msg.next = p; // invariant: p == prev.next
                prev.next = msg;
            }

            // We can assume mPtr != 0 because mQuitting is false.
            if (needWake) {
                nativeWake(mPtr);
            }
        }
        return true;
    }
```
Message的发送实际是放入到了Handler对应线程的MessageQueue中将Message存到了上一个Message.next上形成了一个链式的列表，同时也保证了Message列表的时序性M通过msg.when保证了时序性。所以，至此发送消息就是将消息存放在消息队列中。  
# 2、获取消息  
如果不调用Looper.loop();Handler还是会收不到消息，这个方法就是从消息队列中(Message Queue)中获取消息。  

```  
public static void loop() {  
//熟悉的味道，在分析handler构造函数的时候也会调用myLooper()获取此线程关联的looper
        final Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }  
        //获取Looper对象的消息队列
        final MessageQueue queue = me.mQueue;

        // Make sure the identity of this thread is that of the local process,
        // and keep track of what that identity token actually is.
        Binder.clearCallingIdentity();
        final long ident = Binder.clearCallingIdentity();

        // Allow overriding a threshold with a system prop. e.g.
        // adb shell 'setprop log.looper.1000.main.slow 1 && stop && start'
        final int thresholdOverride =
                SystemProperties.getInt("log.looper."
                        + Process.myUid() + "."
                        + Thread.currentThread().getName()
                        + ".slow", 0);

        boolean slowDeliveryDetected = false;
//死循环，从消息队列中一直取消息
        for (;;) {
            Message msg = queue.next(); // might block
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }

            // This must be in a local variable, in case a UI event sets the logger
            final Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }

            final long traceTag = me.mTraceTag;
            long slowDispatchThresholdMs = me.mSlowDispatchThresholdMs;
            long slowDeliveryThresholdMs = me.mSlowDeliveryThresholdMs;
            if (thresholdOverride > 0) {
                slowDispatchThresholdMs = thresholdOverride;
                slowDeliveryThresholdMs = thresholdOverride;
            }
            final boolean logSlowDelivery = (slowDeliveryThresholdMs > 0) && (msg.when > 0);
            final boolean logSlowDispatch = (slowDispatchThresholdMs > 0);

            final boolean needStartTime = logSlowDelivery || logSlowDispatch;
            final boolean needEndTime = logSlowDispatch;

            if (traceTag != 0 && Trace.isTagEnabled(traceTag)) {
                Trace.traceBegin(traceTag, msg.target.getTraceName(msg));
            }

            final long dispatchStart = needStartTime ? SystemClock.uptimeMillis() : 0;
            final long dispatchEnd;
            try {  
            //分发消息，target就是消息的handler
                msg.target.dispatchMessage(msg);
                dispatchEnd = needEndTime ? SystemClock.uptimeMillis() : 0;
            } finally {
                if (traceTag != 0) {
                    Trace.traceEnd(traceTag);
                }
            }
            if (logSlowDelivery) {
                if (slowDeliveryDetected) {
                    if ((dispatchStart - msg.when) <= 10) {
                        Slog.w(TAG, "Drained");
                        slowDeliveryDetected = false;
                    }
                } else {
                    if (showSlowLog(slowDeliveryThresholdMs, msg.when, dispatchStart, "delivery",
                            msg)) {
                        // Once we write a slow delivery log, suppress until the queue drains.
                        slowDeliveryDetected = true;
                    }
                }
            }
            if (logSlowDispatch) {
                showSlowLog(slowDispatchThresholdMs, dispatchStart, dispatchEnd, "dispatch", msg);
            }

            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }

            // Make sure that during the course of dispatching the
            // identity of the thread wasn't corrupted.
            final long newIdent = Binder.clearCallingIdentity();
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }

            msg.recycleUnchecked();
        }
     }
```
整个流程就是首先获取当前线程的Looper对象，然后获取Looper对象的消息队列，最后开启一个死循环，不断从消息队列中获取消息，通过消息的handler分发消息msg.target.dispatchMessage(msg)。下面看下Handler类中的这个函数。  
  
```/**
     * Handle system messages here.
     */
    public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
   }
```
这个函数调用handleCallback(msg)或者handleMessage(msg)处理消息。  
## 3、消息处理  
本例中我们没有设置callback，所以直接调用的是handleMessage(msg)这个函数。也就是我们在创建handler的时候复写的方法。但是这儿有个handleCallback(msg)方法。  
查了下资料发现这个是由于Handler发送消息的方式导致有两种方式处理消息。如果我们使用sendMessage方式发送消息，则callback为空，即回调我们复写的handleMessage方法。二另外一种方式发送消息就是post(Runnable r)方法发送消息，就会回调runnable中复写的run方法。修改我们demo中的代码使用post方法发送消息。  

```  
private void handlerMessageInTwoThread() {
        Thread firstThread = new Thread() {
            @Override
            public void run() {
                Looper.prepare();
                handler = new Handler();
                Looper.loop();
            }
        };

        Thread secondThread = new Thread() {
            @Override
            public void run() {
                final Message sendMessage = handler.obtainMessage();
                sendMessage.obj = "secondThread发送的消息";
                Log.d("secondThread","secondThread sendMessage: " + sendMessage.obj);
                //handler.sendMessage(sendMessage);
                handler.post(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(MainActivity.this,"firstThread handleMessage" + sendMessage.obj,Toast.LENGTH_LONG).show();
                    }
                });

            }
        };
        firstThread.setName("firstThread");
        firstThread.start();
        secondThread.setName("secondThread");
        secondThread.start();
    }
```
在firstThread中创建了handler，并没有复写handleMessage。而是在secondThread中使用post方式发送消息并复写了Runnable的run方法来执行我们需要执行的逻辑。  
# 五、总结  
总结一下，其实Handler机制并没有想象中的复杂，主要就是handler、message、message queue、looper。  
* handler就是对消如果在主线程中创建handler，那么这个handler就和主线程的looper关联了。如果在子线程中创建handler，就需要先调用Looper.prepare()存储此线程的Looper，然后new Handler的时候关联。当然也可以在new Handler的指定关联的looper。  
* message就是存储传递消息的属性。  
* message queue就是存储发送的消息，是一个链式的列表。  
* looper就是从message queue中取出消息给handler处理。  
  
当然handler消息机制中涉及的问题很多，例如内存泄露问题、 ThreadLocal保证线程looper的实现、Android主线程(ActivityThread)问题，这些后续陆续补充。