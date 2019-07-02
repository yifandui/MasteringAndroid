> 文章来源：MasteringAndroid
>
> 作者：StarkSong
>
> 审阅者：
>
> 贡献者：
### 介绍:
1. Android 中实现弹窗的一种方式。
2. 分为 v4 包下的和android.app 包下的，我们使用 v4 包下的， android.app 包下的 DialogFragment 在 Android28 版本上已经被标记为弃用了。
3. 继承与 Fragment ，拥有 Fragment 所有的特性。DialogFragment 里面内嵌了一个 Dialog。
#### 和 Dialog 的区别：
- 相比较 Dialog 来说，DialogFragment 其内嵌了一个 Dialog ，并对它进行一些灵活的管理，并且在 Activity  被异常销毁后重建的时候，DialogFragment 也会跟着重建，单独使用 Dialog 就不会。而且我们可以在 DialogFragment 的 onSaveInstanceState 方法中保存一些我们的数据，DialogFragment 跟着 Activity 重建的时候，从 onRestoreInstanceState 中取出数据，恢复页面显示。
- Dialog 不适合复杂UI，而且不适合弹窗中有网络请求的逻辑开发。而 DialogFragment 可以当做一个 Fragment 来使用，比较适合做一些复杂的逻辑，网络请求。

### 基本使用方式
1. **创建方式：**
    - 重写 `onCreateView` 方法，自定义布局。适用于复杂UI场景。
    - 重写 `onCreateDialog` 方法，自定义Dialog。适用于简单、传统弹窗UI。

2. **重写 onCreateView 方法:**
```java
public class CustomDialogFragment extends DialogFragment {
    private static final String TAG = "CustomDialogFragment";
    private TextView mTvDialogTitle;
    private TextView mTvDialogContent;
    private Button mBtnCancel;
    private Button mBtnConfirm;
    private String content;
    
    public CustomDialogFragment() {
        /*每一个继承了 Fragment 的类都必须有一个空参的构造方法，这样当 Activity 被恢复状态时 Fragment 能够被实例化。
        Google强烈建议我们不要使用构造方法进行传参，因为 Fragment 被实例化的时候，这些带参构造函数不会被调用。如果要
        要传递参数，可以使用 setArguments(bundle) 方式来传参。*/
    }
    
    static CustomDialogFragment newInstance(String content) {
        CustomDialogFragment customDialogFragment = new CustomDialogFragment();
        Bundle bundle = new Bundle();
        bundle.putString("content", content);
        customDialogFragment.setArguments(bundle);
        return customDialogFragment;
    }

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Bundle bundle = getArguments();
        if (bundle != null) {
            content = bundle.getString("content");
        }
    }

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
    //加载布局
        View view = inflater.inflate(R.layout.dialog_coustom, container);
        initView(view);
        return view;
    }
    //初始化View
    private void initView(View view) {
        mTvDialogTitle = view.findViewById(R.id.tv_dialogTitle);
        mTvDialogContent = view.findViewById(R.id.tv_dialogContent);
        mBtnCancel = view.findViewById(R.id.btn_cancel);
        mBtnConfirm = view.findViewById(R.id.btn_confirm);
        mBtnCancel.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                dismiss();
            }
        });
        mBtnConfirm.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                dismiss();
            }
        });
        mTvDialogContent.setText(content);
    }
}
```

3. **重写 onCreateDialog 方法：**

```java
@NonNull
@Override
public Dialog onCreateDialog(@Nullable Bundle savedInstanceState) {
   AlertDialog.Builder builder = new AlertDialog.Builder(getContext());
   builder.setTitle("我是标题");
   builder.setMessage(content);
   builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialog, int which) {
            //处理点击事件
        }
    });
    builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialog, int which) {
            //处理点击事件
        }
    });
    return builder.create();
}
```
> **注意:** AlertDialog 分为 v7 包下的和 android.app 包下的， android.app 包下的在 Android 5.0 以前版本显示为老样式，5.0 以后显示新的 MD 新风格，为了兼容老版本统一显示最新样式，使用 v7 包下的类。

4. **在 Activity 中的显示出来:**

```java
CustomDialogFragment  dialog = CustomDialogFragment.newInstance("我是内容") ;
dialog.show(getSupportFragmentManager(),"dialog");
```
5. **其他**

- 关闭弹窗    `customDialogFragment.dismiss();`
- 去掉标题  `getDialog().requestWindowFeature(Window.FEATURE_NO_TITLE)`
### 自定义宽高样式

#### 自定义宽高
我们在使用 onCreateView 方式创建 DialogFragment 的时候，发现我们在 xml 根布局中设置的宽高并不起作用。这个时候我们可以自己设置 Dialog 所在 Window 的宽高来设置弹窗宽高大小。

具体两种方法：1. 直接指定 window 的宽高。2.在 xml 中设置具体宽高（需要在根布局中再嵌套一层布局）。
 
- **直接指定 window 的宽高**
```java
//CustomDialogFragment 类
@Override
public void onStart() {
    super.onStart();
    Window window = getDialog().getWindow();
    if (window != null) {
        //设置 window 的背景色为透明色.
        //如果通过 window 设置宽高时，想要设置宽为屏宽，就必须调用下面这行代码。
        window.setBackgroundDrawableResource(R.color.transparent);
        WindowManager.LayoutParams attributes = window.getAttributes();
        //在这里我们可以设置 DialogFragment 弹窗的位置
        attributes.gravity = Gravity.START | Gravity.CENTER_VERTICAL;
        //我们可以在这里指定 window的宽高
        attributes.width = 1000;
        attributes.height = 1000;
        //设置 DialogFragment 的进出动画
        attributes.windowAnimations = R.style.DialogAnimation;
        window.setAttributes(attributes);
    }
}
```
>**注：** 如果通过 window 设置弹窗宽高，要注意 `attributes.width = ViewGroup.LayoutParams.MATCH_PARENT` 来设置宽为屏宽时，则必须设置 ` window.setBackgroundDrawableResource()`
- **在 xml 中设置宽高**
    
```java
@Override
    public void onStart() {
        super.onStart();
        Window window = getDialog().getWindow();
        if (window != null) {
            //设置 window 的背景色为透明色.
            window.setBackgroundDrawableResource(R.color.transparent);
            WindowManager.LayoutParams attributes = window.getAttributes();
            //在这里我们可以设置 DialogFragment 弹窗的位置
            attributes.gravity = Gravity.BOTTOM;
            
            /*为什么这里还要设置 window 的宽高呢？
            因为如果 xml 里面的宽高为 match_parent 的时候，window 的宽高也必须是 MATCH_PARENT，否则无法生效！*/
            attributes.width = ViewGroup.LayoutParams.MATCH_PARENT;
            attributes.height = ViewGroup.LayoutParams.WRAP_CONTENT;
            
            //设置 DialogFragment 的进出动画
            attributes.windowAnimations = R.style.DialogAnimation;
            window.setAttributes(attributes);

        }
    }
```

``` xml
<?xml version="1.0" encoding="utf-8"?>

<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    <!--通过 xml 指定宽高的时候，要嵌套一层布局-->
    <!--我们在这里设置宽高为 match_parent 属性的时候, 
    也必须把 window 的宽高设置为 MATCH_PARENT ，否则无法生效！-->
    <android.support.constraint.ConstraintLayout
        android:id="@+id/content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/colorPrimary">

        <TextView
            android:id="@+id/tv_dialogTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="15dp"
            android:text="我是标题"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <TextView
            android:id="@+id/tv_dialogContent"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="15dp"
            android:background="@color/colorPrimary"
            android:text="我是内容"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/tv_dialogTitle" />

        <Button
            android:id="@+id/btn_cancel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="15dp"
            android:layout_marginBottom="100dp"
            android:text="取消"
            app:layout_constraintHorizontal_chainStyle="spread_inside"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toLeftOf="@+id/btn_confirm"
            app:layout_constraintTop_toBottomOf="@+id/tv_dialogContent" />

        <Button
            android:id="@+id/btn_confirm"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="确定"
            app:layout_constraintBottom_toBottomOf="@id/btn_cancel"
            app:layout_constraintLeft_toRightOf="@+id/btn_cancel"
            app:layout_constraintRight_toRightOf="parent" />
    </android.support.constraint.ConstraintLayout>
</FrameLayout>
```

#### 设置样式
我们通常通过 `style(int style,int theme)` 方法来设置Dialog的样式，其中 theme 需要在 `styles.xml` 文件中自定义一个样式，如果不设置样式，直接传 0。这里我们主要说 style 。style 类型总共有四种。

`STYLE_NORMAL` 基本的*普通对话框。默认类型。

`STYLE_NO_TITLE` 对话框无标题。

`STYLE_NO_FRAME`  对话框无边框，无标题。

`STYLE_NO_INPUT` 禁用对话框的所有输入，用户无法触摸它，其窗口将不会接收输入焦点。

> 注意：1、我们在调用 `style(int style,int theme)` 的时候，需要注意的是，这个方法必须在 onCreateView 之前调用，否则是无效的。我们一般在 onCreate 中调用。 2、 如果我们是通过重写 `OnCreateDialog` 方法创建 DialogFragment，我们设置的 theme 主题是不会生效的，需要在 onCreateDialog 方法中重新给 Dialog 设置。
[ setStyle() 调用时机-源码分析](#源码分析)


### 和页面之间传递数据
在我们展示弹窗的时候，可以使用 setArguments(bundle) 方法进行传递参数，也可以使用 FragmentManager 根据 tag 获取 DialogFragment 实例实现通信。`getFragmentManager().findFragmentByTag(tag)`

这里如何将 DialogFragment 的数据回传呢？这里一般分为两种情况：1. DialogFragment 传递数据给 Activity 2. DialogFragment 传递数据给 Fragment 。

 首先定义一个接口
 
```java
public interface OnDialogClickListener {
    void cancel(String msg);
    void confirm(String msg);
}
```
1. 传递数据给宿主 Activity

    Activity 实现 OnDialogClickListener 接口
    ```java
    public class MainActivity extends BaseActivity implements OnDialogClickListener {
    
        private static final String TAG = "MainActivity---";
    
       @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            
            findViewById(R.id.btn_showDialog).setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    //弹出弹窗
                    CustomDialogFragment dialog = CustomDialogFragment.newInstance("我是内容");
                    dialog.show(getSupportFragmentManager(),"dialog");
                }
            });
        }
    
        @Override
        public void cancel(String msg) {
            Log.i(TAG, "cancel: " + msg);
        }
    
        @Override
        public void confirm(String msg) {
            Log.i(TAG, "confirm: " + msg);
        }
    }
    
    ```
    然后在 DialogFragment 中进行回调。
    
    ```java
    mBtnCancel.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            if (getActivity() instanceof OnDialogClickListener) {
                //传递消息给 Activity
                ((OnDialogClickListener) getActivity()).cancel("点击取消");
            }
        }
    });
    mBtnConfirm.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            if (getActivity() instanceof OnDialogClickListener) {
                //传递消息给 Activity 
                ((OnDialogClickListener) getActivity()).confirm("点击确认");
            }
        }
    });
    ```

2. 传递数据给宿主 Fragment

    Activity 实现 OnDialogClickListener 接口
    ```java
    public class MyFragment extends Fragment implements OnDialogClickListener {
        private String TAG = "Fragment=== ";
    
        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            View view = inflater.inflate(R.layout.fragment_my, container, false);
            view.findViewById(R.id.btn_showDialog).setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    //弹出弹窗
                    CustomDialogFragment dialog = CustomDialogFragment.newInstance("我是内容");
                    dialog.show(getChildFragmentManager(),"dialog");
                }
            });
            return view;
        }
    
        @Override
        public void cancel(String msg) {
            Log.i(TAG, "cancel: " + msg);
        }
    
        @Override
        public void confirm(String msg) {
            Log.i(TAG, "confirm: " + msg);
        }
    }
    ```
    然后在 DialogFragment 中进行回调。
    
    ```java
    mBtnCancel.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            //注意：这里调用的是 getParentFragment()，用来获取宿主 Fragment
            if (getParentFragment() instanceof OnDialogClickListener) {
                //传递消息给 Fragment 
                ((OnDialogClickListener) getParentFragment()).cancel("点击取消");
            }
        }
            });
    mBtnConfirm.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            //注意：这里调用的是 getParentFragment()，用来获取宿主 Fragment
            if (getParentFragment() instanceof OnDialogClickListener) {
                //传递消息给 Fragment 
                ((OnDialogClickListener) getParentFragment()).cancel("点击确认");
            }
        }
    });
    ```
    也可以使用 FragmentManager 通过 tag 获取其他 Fragment 的实例，来和其他 Fragment 进行通信。
    ```java
    getFragmentManager().findFragmentByTag(tag);
    ```
    >如果要进行其他复杂场景的数据传递，可以使用 广播、EventBus 等进行通信。

### 源码分析
#### style、theme 的生效时机。
DialogFragment 中 setStyle 的源码如下
```java
//DialogFragment 类：
public void setStyle(@DialogStyle int style, @StyleRes int theme) {
    mStyle = style;
    if (mStyle == STYLE_NO_FRAME || mStyle == STYLE_NO_INPUT) {
        mTheme = android.R.style.Theme_Panel;
    }
    if (theme != 0) {
        mTheme = theme;
    }
}

```
可以看出在 style 为 `STYLE_NO_FRAME` 或 `STYLE_NO_INPUT` 的时候，
如果 mTheme 为 0，就设置 mTheme 为 android.R.style.Theme_Panel；

我们在 DialogFragment 中搜索 mStyle 出现的地方，找到 mStyle 起作用的地方。

```java
@Override
public LayoutInflater onGetLayoutInflater(Bundle savedInstanceState) {
    if (!mShowsDialog) {
        return super.onGetLayoutInflater(savedInstanceState);
    }
    // 在这里调用了 onCreateDialog 方法，创建一个 Dialog.
    mDialog = onCreateDialog (savedInstanceState);

    if (mDialog != null) {
        //在这里调用了 mStyle
        setupDialog(mDialog, mStyle);

        return (LayoutInflater) mDialog.getContext().getSystemService(
                Context.LAYOUT_INFLATER_SERVICE);
    }
    return (LayoutInflater) mHost.getContext().getSystemService(
            Context.LAYOUT_INFLATER_SERVICE);
}
--------------------------------------------------------------------------
/*DialogFragment 本身在创建 dialog 的时候，
调用了 getTheme 方法获取了当前设置的 mTheme，设置给了 Dialog 。
所以如果我们重写覆盖了父类的 onCreateDialog 方法，mTheme 需要我们重新手动设置给 Dialog */
@NonNull
public Dialog onCreateDialog(Bundle savedInstanceState) {
    return new Dialog(getActivity(), getTheme());
}
--------------------------------------------------------------------------
/*从下面的方法可以看出，STYLE_NO_INPUT、STYLE_NO_FRAME、STYLE_NO_TITLE 
这三种类型的 Style 都去掉了 Dialog 的标题。*/
/** @hide */
@RestrictTo(LIBRARY_GROUP)
public void setupDialog(Dialog dialog, int style) {
    switch (style) {
        case STYLE_NO_INPUT:
            dialog.getWindow().addFlags(
                    WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE |
                            WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE);
            // fall through...
        case STYLE_NO_FRAME:
        case STYLE_NO_TITLE:
            dialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
    }
}
```
我们找到 **onGetLayoutInflater** 方法的调用地方

```java
//Fragment 类
@NonNull
LayoutInflater performGetLayoutInflater(@Nullable Bundle savedInstanceState) {
    //这里调用了 onGetLayoutInflater 方法
    LayoutInflater layoutInflater = onGetLayoutInflater(savedInstanceState);
    mLayoutInflater = layoutInflater;
    return mLayoutInflater;
}
```
然后，继续找到 **performGetLayoutInflater** 方法的调用地方，发现在 FragmentManager 中有这么一行代码：
```java
//这里调用了 onGetLayoutInflater 方法，这里的 f 是指具体的 Fragment 实例
f.mView = f.performCreateView(f.performGetLayoutInflater(
                    f.mSavedFragmentState), null, f.mSavedFragmentState);
```
那么，**f.performCreateView**  做了什么呢？

```java
//Fragment 类
View performCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container,
            @Nullable Bundle savedInstanceState) {
    if (mChildFragmentManager != null) {
        mChildFragmentManager.noteStateNotSaved();
    }
    mPerformedCreateView = true;
    //开始调用 Fragment 的 onCreateView 方法.
    return onCreateView(inflater, container, savedInstanceState);
}
```
看到上面，就完全清晰明了了，DialogFragment 中的 onGetLayoutInflater 方法是在 准备调用 onCreateView 方法的时候调用的。 DialogFragment 中的 Dialog 是在执行 onGetLayoutInflater 方法中创建的。并且，mStyle、mTheme 也都是在这个时候生效的。

所以可以得出的结论是：
1. setStyle 要在 onCreateView 之前调用。一般是在 onCreate 中调用。
2. getDialog() 获取 Dialog ，这个方法在 onCreateView 之前调用都是为 null 的。我们可以在 onCreateView 方法中获取 Dialog 实例。
3. 如果是重写 onCreateDialog 方法创建 DialogFragment ，设置的 mTheme 是不起作用的，需要我们在 onCreateDialog 方法中手动设置给 Dialog 。

#### setCancelable 不起作用-原因分析

`setCancelable` 点击弹窗外部消失，并且屏蔽返回键。

`setCanceledOnTouchOutside` 点击弹窗外部不消失，不屏蔽返回键。

一般我们在 DialogFragment 中调用这两个方法的时候，会在 onCreateView 或 onCreateDialog 中调用:
```java
dialog.setCancelable(false);
dialog.setCanceledOnTouchOutside(false);
```
但是，测试的时候你会发现实际结果并未达到期望。
其实，**DialogFragment 本身也有一个 setCancelable 方法**，如果想实现点击外部不消失、屏蔽返回按钮效果，我们要在 onCreateView 和 onCreateDialog 中调用 `CustomDialogFragment.this.setCancelable (false)` 方法。而不是 Dialog 的 setCancelable 方法。**下面是具体分析：**

首先来看 Dialog 的 setCancelable 和 setCanceledOnTouchOutside 方法。

```java
Dialog 类

public void setCancelable(boolean flag) {
    mCancelable = flag;
    updateWindowForCancelable();
}
---------------------------------------------------
public void setCanceledOnTouchOutside(boolean cancel) {
    /*如果设置了点击弹窗外部可消失( cancel 为 true )，首先会查看是否设置了 setCancelable（false），
    如果设置了，就取消这个设置。*/
    if (cancel && !mCancelable) {
        mCancelable = true;
        updateWindowForCancelable();
    }
    mWindow.setCloseOnTouchOutside(cancel);
}
```
然后是 DialogFragment 中 setCancelable 的源码：

```java
DialogFragment 类
// mCancelable 的默认值为ture。
boolean mCancelable = true;

public void setCancelable(boolean cancelable) {
    //将是否能够取消通过 mCancelable 标记起来
    mCancelable = cancelable;
    //如果 mDialog 已经创建了，就直接设置设置给 mDialog 。
    if (mDialog != null) mDialog.setCancelable(cancelable);
}
```
这个时候可能有疑问了，DialogFragment 的 setCancelable 方法内部也是调用了 Dialog 的 setCancelable 方法，为什么这个方法就可以起作用了呢？原因就在于 `mCancelable = cancelable;` 这行代码。

我们通过寻找 mCancelable 调用地方，发下真正的原因所在：

```java
@Override
public void onActivityCreated(Bundle savedInstanceState) {
    //-----代码省略----
    //真正原因就在这里，在 onActivityCreated 方法中又调用了一次 mDialog.setCancelable 方法
    mDialog.setCancelable(mCancelable);
    mDialog.setOnCancelListener(this);
    mDialog.setOnDismissListener(this);
    //-----代码省略----
}
```
因为 mCancelable 这个值默认是 true ，我们在 onCreateView 和 onCreateDialog 中设置 `dialog.setCancelable(false);`后，并没有将 mCancelable 的值改变为false， DialogFragment 在走到 onActivityCreated 生命周期时（++onActivityCreated 在 onCreateView / onCreateDialog 后面执行++），又调用了 `mDialog.setCancelable(mCancelable);` 覆盖了我们之前的设置，所以我们之前的设置没有起作用。DialogFragment 本身的 setCancelable 方法内部改变了 mCancelable 值，所以达到了我们的效果。
### 总结：
1. DialogFragment 继承于 Fragment，内部有一个 Dialog，比直接使用 Dialog 更加的灵活，扩展性也更好。
2. 通过重写 onCreateView 或者 onCreateDialog 来使用 DialogFragment。
3. 通过直接指定 window 的宽高，或者在xml 跟布局中再嵌套一个 ViewGroup 来改变 DialogFragment 的宽高。
4. 通过 getActivity 获取宿主 Activity，从而传递数据给Activity；通过 getParentFragment 获取宿主 Fragment来传递数据给宿主 Fragment。
5. 设置 style和 theme的时候，必须要在 onCreateView 之前调用；getDialog 在 onCreateView 之前调用获取的都是null。
6. 设置弹窗点击外部不可消失和屏蔽返回键要调用 DialogFragment 本身的 setCancelable 方法，而不是 Dialog 的 setCancelable  方法。
>**另，推荐一个好文**[**选择正确的 Fragment#commitXXX() 函数**](http://blog.chengyunfeng.com/?p=1016#ixzz5jIKL0GMm)
### 参考
- [Using DialogFragment](https://github.com/codepath/android_guides/wiki/Using-DialogFragment)
- [Android 官方推荐 : DialogFragment 创建对话框](https://blog.csdn.net/lmj623565791/article/details/37815413)
- [浅析Fragment为什么需要空的构造方法](https://blog.csdn.net/ruancoder/article/details/52001801)
- [Android编程之DialogFragment源码详解](https://blog.csdn.net/xyz_fly/article/details/19303511)