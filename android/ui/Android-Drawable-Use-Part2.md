> 文章来源：MasteringAndroid
>
> 作者：管鹏杰
>
> 审阅者：

接上文，自从 Android 5.0 发布开始，可以看出 Google 越来越重视 Android 系统的 UI 设计风格了，最为明显的就是提出了 Material Design 设计语言。其中包含了很多 UI 设计的新特性，可以说为 Android 系统注入了新鲜的血液。以下文章介绍的都是 Android 5.0 以后引入的 Drawable，一起来看看都有什么吧！

## 11. RippleDrawable

记得谷歌刚发布 Android 5.0 系统时，用 API 21 的镜像启动模拟器后看到一个很明显的变化就是，很多按钮上都加了点击波纹效果，

### 11.1 语法

```xml
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
        android:color="color"
        android:radius="dimension">
    <item
        android:id="@[package:]id/resource_name"
        android:drawable="@[package:]drawable/drawable_resource"
        android:top="dimension"
        android:right="dimension"
        android:bottom="dimension"
        android:left="dimension"
        android:gravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
                          "fill_vertical" | "center_horizontal" | "fill_horizontal" |
                          "center" | "fill" | "clip_vertical" | "clip_horizontal"] />

</ripple>
```

RippleDrawable 顶层标签为 **\<ripple\>**，它的两个属性的含义分别是：

**android:color**

ripple 效果的颜色

**android:radius**

ripple 完全扩散开始的半径。默认会根据容器大小来计算。

除此之外，它可以包含多个 **\<item\>** 标签，每个 item 表示一个 Drawable，item 的属性含义分别是：

|                           属性                           |                             含义                             |
| :------------------------------------------------------: | :----------------------------------------------------------: |
|                     android:drawable                     |            drawable 资源，可引用现有的的 Drawable            |
|                        android:id                        | 如果 item 的 id 设置成 **@android:id/mask**，在初始化时这个 item 不会被绘制，只会在点击的时候以蒙层的形式限制波纹的范围在这个 item 之内 |
| android:top、android:right、android:bottom、android:left |           Drawable 相对于 View 在各个方向的偏移量            |
|                     android:gravity                      |             尺寸小于容器尺寸时在容器中的摆放位置             |

### 11.2 用法示例

**定义**

```xml
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
        android:color="@color/colorAccent"
        android:radius="90dp">

    <item
            android:id="@android:id/mask"
            android:drawable="@android:color/white" />

    <item android:drawable="@color/colorPrimary" />

</ripple>
```

**使用**

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
            android:text="I' m a Button"
            android:layout_width="200dp"
            android:layout_height="50dp"
            android:background="@drawable/drawable_ripple"
            android:id="@+id/button"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

**效果图**

![ripple-drawable](https://my-bucket-1251125515.cos.ap-guangzhou.myqcloud.com/Blog-Article/Android-Drawable-Use/ripple-drawable.gif)

## 12. VectorDrawable

从 API 21(Android 5.0) 开始，Google 开始支持使用 Vector，VectorDrawable 应运而生。相比于普通的 Drawable，它具有以下优点：

- Vector 图像可以自动进行适配，不需要通过分辨率来设置不同的图片
- Vector 图像可以大幅减少图像的体积，同样一张图，用 Vector 来实现，可能只有 PNG 的几十分之一
- 使用简单，很多设计工具都可以直接导出 SVG 图像，从而转换成 Vector 图像
- 功能强大，不用写很多代码就可以实现非常复杂的动画
- 成熟、稳定，目前已经非常广泛地进行使用了

### 12.1 语法

定义一个 VectorDrawable 的语法如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:name="string"
    android:width="dimension"
    android:height="dimension"
    android:viewportHeight="float"
    android:viewportWidth="float"
    android:tint="color"
    android:tintMode=["add" | "multiply" | "src_top" | "src_in" | "src_over" | "screen"]
    android:autoMirrored=["true" | "false"]
    android:alpha="integer" />
    <group
        android:name="string"
        android:pivotX="float"
        android:pivotY="float"
        android:rotation="integer"
        android:translationX="float"
        android:translationY="float"
        android:scaleX="float"
        android:scaleY="float">
        <path
            android:name="string"
            android:pathData="path"
            android:fillColor="color"
            android:fillAlpha="integer"
            android:strokeColor="color"
            android:strokeWidth="integer"
            android:strokeAlpha="integer"
            android:trimPathStart="float"
            android:trimPathEnd="float"
            android:trimPathOffset="float"
            android:strokeLineCap=["butt" | "round" | "square"]
            android:strokeLineJoin=["round" | "bevel" | "miter"]
            android:strokeMiterLimit="integer"
            android:fillType=["nonZero" | "evenOdd"] />
        <clip-path
            android:name="string"
            android:pathData="path" />
    </group>
</vector>
```

VectorDrawable 的根标签为 **\<vector>**，老规矩，先看看它的子元素属性和含义：

|                     属性                      |                             含义                             |
| :-------------------------------------------: | :----------------------------------------------------------: |
|                 android:name                  |                       drawable 的名字                        |
|         android:width、android:height         |            内部(intrinsic)宽度和高度。一般使用 dp            |
| android:viewportWidth、android:viewportHeight | 矢量图视图的宽度和高度。视图就是矢量图 path 路径数据所绘制的虚拟画布 |
|                 android:tint                  |                         给矢量图着色                         |
|               android:tintMode                | 着色模式。共支持六种模式，默认为“src_in"，详情请参考 [PorterDuff.Mode](https://www.jianshu.com/p/d11892bbe055) |
|             android:autoMirrored              |                           自动翻转                           |
|                 android:alpha                 |      图片透明度。取值范围为 [0, 255]VectorDrawble 支持       |

一张矢量图可以由多个 **path** 组成，**\<group\>** 标签可以对多个 path 进行分组，标签内的属性值对组内所有 path 都生效，<group\> 标签的各个属性及其含义分别为如下：

|                    属性                    |                             含义                             |
| :----------------------------------------: | :----------------------------------------------------------: |
|                android:name                |                          分组的名字                          |
|       android:pivotX、android:pivotY       | 缩放和旋转时候的 X 和 Y 的基准点。该值是相对于 vector 的 viewport 值来指定的 |
| android:translationX、android:translationY | X 轴和 Y 轴方向的平移位移。该值同样是相对于 viewport 值来指定的 |
|              android:rotation              |                           旋转角度                           |
|       android:scaleX、android:scaleY       |               分别在 X 轴和 Y 轴方向的缩放比例               |

接下来就是 **\<path\>** 标签了，<path\> 标签定了的矢量图的绘制方法，包括绘制路径、颜色、边框样式等属性，它的所有属性及其含义如下：

|           属性           |                             含义                             |
| :----------------------: | :----------------------------------------------------------: |
|                          |                                                              |
|     android:pathData     | path 指令。指令格式参考：[路径](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial/Paths) |
|    android:fillColor     | path 填充颜色。一般为纯色，API 24 开始支持 Gradient 渐变色，详情请参考：[vectordrawable-gradients-part1](https://blog.stylingandroid.com/vectordrawable-gradients-part1/) 和 [vectordrawable-gradients-part1-2/](https://blog.stylingandroid.com/vectordrawable-gradients-part1-2/) |
|    android:fillAlpha     | X 轴和 Y 轴方向的平移位移。该值同样是相对于 viewport 值来指定的 |
|     android:fillType     | path 的填充模式。默认是"noneZero"，详情参考：[非零环绕数规则和奇-偶规则](https://blog.csdn.net/freshforiphone/article/details/8273023) 和 [Android 关于Path的FillType](https://www.jianshu.com/p/ce808a9e7e38) |
|   android:strokeWidth    |                        path 边框宽度                         |
|   android:strokeColor    |                        path 边框颜色                         |
|   android:strokeAlpha    |                       path 边框透明度                        |
|  android:strokeLineCap   | path 线头的形状。`buff` 平头、`round` 圆头和 `square` 方头。默认为 `buff` |
|  android:strokeLineJoin  | path 拐角的形状。`miter` 尖角、 `bevel` 平角和 `round` 圆角。默认为 `miter` |
| android:strokeMiterLimit | 设置拐角的形状为 `miter` 时，拐角的延长线的最大值。当小到一定程度时，miter 效果将会失效从而变成 bevel 效果 |
|  android:trimPathStart   |       从 path 起始位置截断路径的比率。取值范围为[0, 1]       |
|   android:trimPathEnd    |       从 path 结束位置截断路径的比率。取值范围为[0, 1]       |
|  android:trimPathOffset  | path 截取起点的偏移量。取值范围为[0, 1]，由于 path 的起点和终点可以看作的首尾相连的，因此起点和终点是一起发生偏移的 |

### 12.2 用法示例

**定义**

```xml
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:width="200dp"
        android:height="200dp"
        android:viewportHeight="600"
        android:viewportWidth="600">
    <path
            android:strokeWidth="15"
            android:strokeColor="#000000"
            android:strokeLineCap="butt"
            android:pathData="M5 10 l200 0"/>
    <!--路径起点/终点往后偏移 10%，从新起点开始往后截断至 50% 的路径-->
    <path
            android:strokeWidth="15"
            android:strokeColor="#000000"
            android:strokeLineCap="butt"
            android:trimPathStart="0.5"
            android:trimPathOffset="0.1"
            android:pathData="M5 80 l200 0"/>
    <!--路径起点/终点往后偏移 50%，从新终点往前截断至 70% 部分-->
    <!--也可以理解为从新起点往后截取至 70% 部分-->
    <path
            android:strokeWidth="15"
            android:strokeColor="#000000"
            android:strokeLineCap="butt"
            android:trimPathEnd="0.7"
            android:trimPathOffset="0.5"
            android:pathData="M5 150 l200 0"/>

    <path
            android:strokeWidth="15"
            android:strokeColor="#000000"
            android:strokeLineCap="square"
            android:strokeLineJoin="round"
            android:pathData="M5 230 l200 0 l-100 30"/>
    <path
            android:strokeWidth="15"
            android:strokeColor="#000000"
            android:strokeLineCap="butt"
            android:strokeLineJoin="miter"
            android:pathData="M5 290 l200 0 l-100 30"/>
    <path
            android:strokeWidth="15"
            android:strokeColor="#000000"
            android:strokeLineCap="round"
            android:strokeLineJoin="miter"
            android:strokeMiterLimit="7"
            android:pathData="M5 350 l200 0 l-100 30"/>

    <group
            android:name="name"
            android:translateX="10"
            android:translateY="10"
            android:rotation="90"
            android:pivotX="300"
            android:pivotY="300">
        <path
                android:name="noneZero"
                android:strokeWidth="2"
                android:strokeColor="#ffffff"
                android:fillColor="#3C8FC1"
                android:pathData="M20 120 a100 100 0 1 1 200 0 a100 100 0 1 1 -200 0
            M40 120 a80 80 0 1 1 160 0 a80 80 0 1 1 -160 0"/>
        <path
                android:name="evenOdd"
                android:strokeWidth="5"
                android:strokeColor="#ffffff"
                android:strokeAlpha="128"
                android:fillColor="#3C8FC1"
                android:fillType="evenOdd"
                android:pathData="M260 120 a100 100 0 1 1 200 0 a100 100 0 1 1 -200 0
            M280 120 a80 80 0 1 1 160 0 a80 80 0 1 1 -160 0"/>
    </group>
</vector>
```

**使用**

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.appcompat.widget.AppCompatImageView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:srcCompat="@drawable/drawable_vector"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

**效果图**

![vector-drawable](https://my-bucket-1251125515.cos.ap-guangzhou.myqcloud.com/Blog-Article/Android-Drawable-Use/vector-drawable.jpg)

### 12.3 兼容性问题

由于 VectorDrawable 是从 Android 5.0 之后引入的，如果想要在旧设备上运行，就必须要进行兼容性设置，由于篇幅有限，可以参考[Android Vector曲折的兼容之路](https://blog.csdn.net/eclipsexys/article/details/51838119)，这里不再赘述。

## 13. AnimatedVectorDrawable

当你以为 VectorDrawable 除了替代传统图标别无它用那你就实在 too young 了，与 VetorDrawable 一起诞生的还有 **AnimatedVectorDrawable**。还记得 VectorDrawable 中的 `group`  和 `path` 有个 **name** 属性吗？这时候它们就派上用场了，AnimatedVectorDrawable 可以通过 name 属性为 group 和 path 绑定一个属性动画，让这些 path 可以动起来，做出比较炫酷的动画效果。在 API 25 之前，因为渲染是在 UI 线程进行的的，因此性能不是很好，加上兼容性问题，目前使用得并不多。自从 API 25 之后，Google 将 AnimatedVectorDrawable 的渲染放在了 RenderThered 中执行，这显然减轻了不少 UI 线程的压力，Google 官方描述是：

> This means animations in AnimatedVectorDrawable can remain smooth even when there is heavy workload on the UI thread. 

因此，如果运行在新设备上，大家大可不必操心性能问题了。

### 13.1 语法

```xml
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
     android:drawable="@[package:]drawable/drawable_resource" >
     <target
         android:name="string"
         android:animation="@[package:]animator/animator_resource" />
 </animated-vector>
```

AnimatedVectorDrawable 的根标签为 **\<animated-vector\>**，`android:drawable` 属性用来指定 VectorDrawable 资源，**\<target\>** 标签将它的子元素 `name` 属性指定的 VectorDrawable 中需要添加动画效果的 path 或者 group 与 `animation` 属性中的 animator 资源绑定起来。animation 资源同样可以通过标签定义或者指向现有的 animator 文件。

### 13.2 用法示例

下面以前面文章中提到的 Demo 中的一个效果为例，展示一个 AnimatedVectorDrawable 的基本用法。

**定义**

需要添加动画效果的 VectorDrawable，一共有两个 path：

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:aapt="http://schemas.android.com/aapt"
        android:drawable="@drawable/ic_arrow">

    <target android:name="left">
        <aapt:attr name="android:animation">
            <objectAnimator
                    android:duration="1000"
                    android:interpolator="@android:interpolator/anticipate_overshoot"
                    android:propertyName="translateX"
                    android:repeatCount="infinite"
                    android:repeatMode="reverse"
                    android:valueFrom="0"
                    android:valueTo="-10"
                    android:valueType="floatType"/>
        </aapt:attr>
    </target>

    <target android:name="right">
        <aapt:attr name="android:animation">
            <objectAnimator
                    android:duration="1000"
                    android:interpolator="@android:interpolator/anticipate_overshoot"
                    android:propertyName="translateX"
                    android:repeatCount="infinite"
                    android:repeatMode="reverse"
                    android:valueFrom="0"
                    android:valueTo="10"
                    android:valueType="floatType"/>
        </aapt:attr>
    </target>

</animated-vector>
```

**使用**

在代码中监听和控制动画：

```kotlin
class AnimatedVectorDrawableActivity : AppCompatActivity() {
    private lateinit var animatable2Compat: Animatable2Compat

    @RequiresApi(Build.VERSION_CODES.M)
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_animated_vector_drawable)

        var image = findViewById<ImageView>(R.id.image)
        var animatedVectorDrawableCompat = AnimatedVectorDrawableCompat.create(this, R.drawable.drawable_animated_vector)
        image.setImageDrawable(animatedVectorDrawableCompat)
        animatable2Compat = image.drawable as Animatable2Compat
        animatable2Compat.registerAnimationCallback(object : Animatable2Compat.AnimationCallback() {
            override fun onAnimationStart(drawable: Drawable?) {
                Log.e("gpj", "onAnimationStart")
            }
            override fun onAnimationEnd(drawable: Drawable?) {
                Log.e("gpj", "onAnimationEnd")
            }
        })
        animatable2Compat.start()
    }

    override fun onDestroy() {
        super.onDestroy()
        animatable2Compat.stop()
    }
}
```

**效果图**

![animated-vector-drawable](https://my-bucket-1251125515.cos.ap-guangzhou.myqcloud.com/Blog-Article/Android-Drawable-Use/animated-vector-drawable.gif)

## 14. AnimatedStateListDrawable

前面提到的 StateListDrawable 只能使用静态的资源在不同的状态之间进行切换，同样的，在 Android 5.0 之后，状态列表里可以使用动态资源了，它就是 **AnimatedStateListDrawable**。

### 14.1 语法

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android"
    android:constantSize=["true" | "false"]
    android:dither=["true" | "false"]
    android:variablePadding=["true" | "false"] 
    android:autoMirrored=["true" | "false"] 
    android:enterFadeDuration="integer"
    android:exitFadeDuration="integer">
    <item
        android:id="@[+][package:]id/resource_name"
        android:drawable="@[package:]drawable/drawable_resource"
        android:state_pressed=["true" | "false"]
        android:state_focused=["true" | "false"]
        android:state_hovered=["true" | "false"]
        android:state_selected=["true" | "false"]
        android:state_checkable=["true" | "false"]
        android:state_checked=["true" | "false"]
        android:state_enabled=["true" | "false"]
        android:state_activated=["true" | "false"]
        android:state_window_focused=["true" | "false"] />
    <transition
        android:drawable="@[package:]drawable/drawable_resource"
        android:fromId="@[package:]id/item_name"
        android:toId="@[package:]id/item_name" />
</selector>
```

可以发现，相对于 StateListDrawable，这里只多出一个 **\<transition\>** 标签，它的各个属性含义分别是：

**android:drawable**

定义或者指向一个 AnimatedVectorDrawable 资源。不难理解，这里需要指定状态变化的动画。

**android:fromId** 和 **android:toId**

分别指定状态变化的起始和结束 item 的 id。详情请看示例。

### 14.2 用法示例

**定义**

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-selector
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:visible="true"
        android:dither="true">

    <!--勾选状态-->
    <item
            android:id="@+id/checked"
            android:drawable="@drawable/ic_checked"
            android:state_checked="true" />

    <!--未勾选状态-->
    <item
            android:id="@+id/unchecked"
            android:drawable="@drawable/ic_unchecked" />

    <!--未勾选状态过度到勾选状态-->
    <transition
            android:drawable="@drawable/toggle_unchecked_checked"
            android:fromId="@id/unchecked"
            android:toId="@id/checked" />

    <!--勾选状态过度到未勾选状态-->
    <transition
            android:drawable="@drawable/toggle_checked_unchecked"
            android:fromId="@id/checked"
            android:toId="@id/unchecked" />

</animated-selector>
```

正常状态到勾选状态过度动画：

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
                 xmlns:aapt="http://schemas.android.com/aapt"
                 android:drawable="@drawable/ic_checked">

    <!--打勾 path 动画-->
    <target android:name="tick">
        <aapt:attr name="android:animation">
            <objectAnimator
                    android:duration="200"
                    android:interpolator="@android:interpolator/accelerate_cubic"
                    android:propertyName="trimPathEnd"
                    android:valueFrom="0"
                    android:valueTo="1"
                    android:valueType="floatType" />
        </aapt:attr>
    </target>

    <!--圆圈 path 动画-->
    <target android:name="circle">
        <aapt:attr name="android:animation">
            <objectAnimator
                    android:duration="500"
                    android:interpolator="@android:interpolator/accelerate_decelerate"
                    android:propertyName="strokeColor"
                    android:valueFrom="#A0A0A0"
                    android:valueTo="#1E9618"
                    android:valueType="intType" />
        </aapt:attr>
    </target>
</animated-vector>

```

勾选状态到未勾选状态过度动画：

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
                 xmlns:aapt="http://schemas.android.com/aapt"
                 android:drawable="@drawable/ic_checked">

    <!--打勾 path 动画-->
    <target android:name="tick">
        <aapt:attr name="android:animation">
            <objectAnimator
                    android:duration="100"
                    android:interpolator="@android:interpolator/decelerate_cubic"
                    android:propertyName="trimPathEnd"
                    android:valueFrom="1"
                    android:valueTo="0"
                    android:valueType="floatType" />
        </aapt:attr>
    </target>

    <!--圆圈 path 动画-->
    <target android:name="circle">
        <aapt:attr name="android:animation">
            <objectAnimator
                    android:duration="500"
                    android:interpolator="@android:interpolator/accelerate_decelerate"
                    android:propertyName="strokeColor"
                    android:valueFrom="#1E9618"
                    android:valueTo="#A0A0A0"
                    android:valueType="intType" />
        </aapt:attr>
    </target>
</animated-vector>

```

**使用**

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.appcompat.widget.AppCompatCheckBox
            android:layout_width="wrap_content"
            android:layout_height="50dp"
            android:button="@drawable/drawable_animated_state_list"
            android:paddingEnd="8dp"
            android:paddingLeft="8dp"
            android:paddingRight="8dp"
            android:paddingStart="8dp"
            android:text="I'm a CheckBox"
            android:textColor="#ff00ff"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

```

**效果图**

![](https://my-bucket-1251125515.cos.ap-guangzhou.myqcloud.com/Blog-Article/Android-Drawable-Use/animated-state-list-drawable.gif)

## 15. AnimatedImageDrawable

大家都知道，在 Android 8.0 之前，如果我们要在设备上显示 GIF 图，一般都要借助一些第三方的库（如 Glide）来实现。记得 Android 8.0 刚发布的时候，提到一个 [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) 类，它除了可以解析 PNG、JEPG 类型的文件之外，还可以解析 WebP 和 GIF，而 GIF 文件解析出来的正是 AnimatedImageDrawable。由于目前资源有限，Google 也没有提供使用 XML 来定义 AnimatedImageDrawable   的例子，这里就在 Kotlin 代码里面介绍 AnimatedImageDrawable 和 ImageDecoder 的简单使用。

**使用**

```kotlin
@RequiresApi(Build.VERSION_CODES.P)
class AnimatedImageDrawableActivity : AppCompatActivity() {
    var mAnimatedImageDrawable: AnimatedImageDrawable? = null

    private val cacheAsset: CacheAsset by lazy {
        CacheAsset(this)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_animated_image_drawable)

        button.setOnClickListener {
            ImageDecoder.createSource(cacheAsset.file("gif_example.gif")).also { source ->
                ImageDecoder.decodeDrawable(source).also { drawable ->
                    image.setImageDrawable(drawable)
                    if(drawable is AnimatedImageDrawable) {
                        mAnimatedImageDrawable = drawable
                        drawable.start()
                    }
                }
            }
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        mAnimatedImageDrawable?.run { stop() }
    }
}
```

**效果图**

![animated-image-drawable](https://my-bucket-1251125515.cos.ap-guangzhou.myqcloud.com/Blog-Article/Android-Drawable-Use/animated-image-drawable.gif)

到此，常见的 Drawable 的用法已经全部讲完了，如果要加深理解，建议把 Demo 跑一遍。

**附：文章中的 Demo 地址：[https://github.com/guanpj/DrawableDemo](https://github.com/guanpj/DrawableDemo)**