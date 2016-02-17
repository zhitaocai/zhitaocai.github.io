title: 解决小米MIUI系统上后台应用没法弹Toast的问题
tags:
  - Android 系统兼容
categories:
  - Android Develop
date: 2016-02-09 07:42:13
---
![](/img/Momentum/201602090742.jpg)

早~~，大年初二，一大早就被喉咙痛痛醒（夜晚睡觉之前不要饮太多雪碧），实在受不了，就起床了，想起前段时间在小米系统上遇到的一个诡异问题，于是趁这个机会码一下

<!--more-->

## 问题

某一天发现在小米的MIUI系统上，一个好好的Toast总是不能弹出来，写法是没有问题的，直到发现log中出现一个``invisible to user``的输出，几经google，百度，解决方案没找到，倒是在 [小米开发者论坛帖子](http://www.miui.com/thread-2628717-1-1.html)中找到发现相同问题的同学们T_T
![](/img/MIUI/toast-problem.png)

问题明确了：**后台应用在小米MIUI系统（没有仔细测试每个版本，应该是自某个版本开始的，大概测试的时候，低版本miui还是正常运行，高版本的就不能）上没有办法弹出Toast。**

## 解决

经过一番看源码和在某一篇[关于Toast源码分析的博文](http://www.cnblogs.com/net168/p/4058193.html)中了解到

Toast的弹出流程：

1. 通过``new Toast(Context context)``或者``makeText(...)``方法实例化Toast对象
2. 调用``show()``方法之后，实例会加入到一个TN变量(AIDL)的服务队列中，而这个队列由系统维护
3. TN控制Toast的显示和消息

于是就好猜测了，MIUI上可能是出于**"绿化"**的考虑，在维护Toast队列的时候，Toast只能在自己的进程运行在顶端的时候才能弹出来，否则就**"invisible to user"**.

那么尝试性的解决方案就有了：**我们自己参照Toast的源码，重写一份，最后show的时候，不进入TN维护的队列，我们自己用Handler+Queue来维护Toast的消息队列**

```java
    package io.github.zhitaocai.toastcompat.toastcompat;

    import android.annotation.TargetApi;
    import android.content.Context;
    import android.content.res.Configuration;
    import android.graphics.PixelFormat;
    import android.os.Build;
    import android.os.Handler;
    import android.view.Gravity;
    import android.view.View;
    import android.view.WindowManager;
    import android.widget.TextView;
    import android.widget.Toast;

    import java.util.concurrent.BlockingQueue;
    import java.util.concurrent.LinkedBlockingQueue;
    import java.util.concurrent.atomic.AtomicInteger;

    import io.github.zhitaocai.toastcompat.util.DisplayUtil;

    /**
     * @author zhitao
     * @since 2016-01-21 14:33
     */
    public class MIUIToast implements IToast {

        private static Handler mHandler = new Handler();

        /**
         * 维护toast的队列
         */
        private static BlockingQueue<MIUIToast> mQueue = new LinkedBlockingQueue<MIUIToast>();

        /**
         * 原子操作：判断当前是否在读取{@linkplain #mQueue 队列}来显示toast
         */
        protected static AtomicInteger mAtomicInteger = new AtomicInteger(0);

        private WindowManager mWindowManager;

        private long mDurationMillis;

        private View mView;

        private WindowManager.LayoutParams mParams;

        private Context mContext;

        public static IToast makeText(Context context, String text, long duration) {
            return new MIUIToast(context).setText(text).setDuration(duration)
                    .setGravity(Gravity.BOTTOM, 0, DisplayUtil.dip2px(context, 64));
        }

        public MIUIToast(Context context) {

            mContext = context;
            mWindowManager = (WindowManager) mContext.getSystemService(Context.WINDOW_SERVICE);
            mParams = new WindowManager.LayoutParams();
            mParams.height = WindowManager.LayoutParams.WRAP_CONTENT;
            mParams.width = WindowManager.LayoutParams.WRAP_CONTENT;
            mParams.format = PixelFormat.TRANSLUCENT;
            mParams.windowAnimations = android.R.style.Animation_Toast;
            mParams.type = WindowManager.LayoutParams.TYPE_TOAST;
            mParams.setTitle("Toast");
            mParams.flags = WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON | WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE |
                            WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE;
            // 默认小米Toast在下方居中
            mParams.gravity = Gravity.BOTTOM | Gravity.CENTER_HORIZONTAL;
        }

        /**
         * Set the location at which the notification should appear on the screen.
         *
         * @param gravity
         * @param xOffset
         * @param yOffset
         */
        @TargetApi(Build.VERSION_CODES.JELLY_BEAN_MR1)
        @Override
        public IToast setGravity(int gravity, int xOffset, int yOffset) {

            // We can resolve the Gravity here by using the Locale for getting
            // the layout direction
            final int finalGravity;
            if (Build.VERSION.SDK_INT >= 14) {
                final Configuration config = mView.getContext().getResources().getConfiguration();
                finalGravity = Gravity.getAbsoluteGravity(gravity, config.getLayoutDirection());
            } else {
                finalGravity = gravity;
            }
            mParams.gravity = finalGravity;
            if ((finalGravity & Gravity.HORIZONTAL_GRAVITY_MASK) == Gravity.FILL_HORIZONTAL) {
                mParams.horizontalWeight = 1.0f;
            }
            if ((finalGravity & Gravity.VERTICAL_GRAVITY_MASK) == Gravity.FILL_VERTICAL) {
                mParams.verticalWeight = 1.0f;
            }
            mParams.y = yOffset;
            mParams.x = xOffset;
            return this;
        }

        @Override
        public IToast setDuration(long durationMillis) {
            if (durationMillis < 0) {
                mDurationMillis = 0;
            }
            if (durationMillis == Toast.LENGTH_SHORT) {
                mDurationMillis = 2000;
            } else if (durationMillis == Toast.LENGTH_LONG) {
                mDurationMillis = 3500;
            } else {
                mDurationMillis = durationMillis;
            }
            return this;
        }

        /**
         * 不能和{@link #setText(String)}一起使用，要么{@link #setView(View)} 要么{@link #setView(View)}
         *
         * @param view
         *
         * @return
         */
        @Override
        public IToast setView(View view) {
            mView = view;
            return this;
        }

        @Override
        public IToast setMargin(float horizontalMargin, float verticalMargin) {
            mParams.horizontalMargin = horizontalMargin;
            mParams.verticalMargin = verticalMargin;
            return this;
        }

        /**
         * 不能和{@link #setView(View)}一起使用，要么{@link #setView(View)} 要么{@link #setView(View)}
         *
         * @return
         */
        @Override
        public IToast setText(String text) {

            // 模拟Toast的布局文件 com.android.internal.R.layout.transient_notification
            // 虽然可以手动用java写，但是不同厂商系统，这个布局的设置好像是不同的，因此我们自己获取原生Toast的view进行配置

            View view = Toast.makeText(mContext, text, Toast.LENGTH_SHORT).getView();
            if (view != null) {
                TextView tv = (TextView) view.findViewById(android.R.id.message);
                tv.setText(text);
                setView(view);
            }

            return this;
        }

        @Override
        public void show() {
            // 1. 将本次需要显示的toast加入到队列中
            mQueue.offer(this);

            // 2. 如果队列还没有激活，就激活队列，依次展示队列中的toast
            if (0 == mAtomicInteger.get()) {
                mAtomicInteger.incrementAndGet();
                mHandler.post(mActivite);
            }
        }

        @Override
        public void cancel() {
            // 1. 如果队列已经处于非激活状态或者队列没有toast了，就表示队列没有toast正在展示了，直接return
            if (0 == mAtomicInteger.get() && mQueue.isEmpty()) {
                return;
            }

            // 2. 当前显示的toast是否为本次要取消的toast，如果是的话
            // 2.1 先移除之前的队列逻辑
            // 2.2 立即暂停当前显示的toast
            // 2.3 重新激活队列
            if (this.equals(mQueue.peek())) {
                mHandler.removeCallbacks(mActivite);
                mHandler.post(mHide);
                mHandler.post(mActivite);
            }

            //TODO 如果一个Toast在队列中的等候展示，当调用了这个toast的取消时，考虑是否应该从对队列中移除，看产品需求吧
        }

        private void handleShow() {
            if (mView != null) {
                if (mView.getParent() != null) {
                    mWindowManager.removeView(mView);
                }
                mWindowManager.addView(mView, mParams);
            }
        }

        private void handleHide() {
            if (mView != null) {
                // note: checking parent() just to make sure the view has
                // been added...  i have seen cases where we get here when
                // the view isn't yet added, so let's try not to crash.
                if (mView.getParent() != null) {
                    mWindowManager.removeView(mView);
                    // 同时从队列中移除这个toast
                    mQueue.poll();
                }
                mView = null;
            }
        }

        private static void activeQueue() {
            MIUIToast miuiToast = mQueue.peek();
            if (miuiToast == null) {
                // 如果不能从队列中获取到toast的话，那么就表示已经暂时完所有的toast了
                // 这个时候需要标记队列状态为：非激活读取中
                mAtomicInteger.decrementAndGet();
            } else {

                // 如果还能从队列中获取到toast的话，那么就表示还有toast没有展示
                // 1. 展示队首的toast
                // 2. 设置一定时间后主动采取toast消失措施
                // 3. 设置展示完毕之后再次执行本逻辑，以展示下一个toast
                mHandler.post(miuiToast.mShow);
                mHandler.postDelayed(miuiToast.mHide, miuiToast.mDurationMillis);
                mHandler.postDelayed(mActivite, miuiToast.mDurationMillis);
            }
        }

        private final Runnable mShow = new Runnable() {
            @Override
            public void run() {
                handleShow();
            }
        };

        private final Runnable mHide = new Runnable() {
            @Override
            public void run() {
                handleHide();
            }
        };

        private final static Runnable mActivite = new Runnable() {
            @Override
            public void run() {
                activeQueue();
            }
        };

    }
```

完整源码地址在[这里](https://github.com/zhitaocai/ToastCompat)

## 参考资料

* [Android：谈一谈安卓应用中的Toast情节（基础）](http://www.cnblogs.com/net168/p/4041763.html)
* [Android：剖析源码，随心所欲控制Toast显示](http://www.cnblogs.com/net168/p/4058193.html)
* [Android应用系列：仿MIUI的Toast动画效果实现（有图有源码）](http://www.cnblogs.com/net168/p/4237528.html)

