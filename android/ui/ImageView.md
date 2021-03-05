
# 知识点简介
> 文章来源：MasteringAndroid
>
> 作者：高祥
>
> 审阅者：
>
> 贡献者：



ImageView在官方的介绍上说是显示任意图像，如图标。

ImageView类可以从各种来源(如资源或内容提供程序)加载图像，负责从图像中计算其度量，以便可以在任何布局管理器中使用，并提供各种显示选项，如缩放和着色。

# 基本使用方法
1. xml
xml中声明ImageView控件，然后在activity中通过findViewById()获取

```
    <ImageView
        android:id="@+id/image"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" 
        android:src="@mipmap/icon_check"/>
```

```
public class MainActivity extends RxAppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ImageView imageView = (ImageView) findViewById(R.id.image);
    }

}
```
2. new
通过new创建出ImageView对象，传入一个context

```
  ImageView view = new ImageView(this);
```
# 常用属性
## 常用属性详解
- android:adjustViewBounds

&emsp;相关方法:setAdjustViewBounds(boolean)
&emsp;官方解释：通过调整ImageView的界限来保持图片的宽高比例

&emsp;adjustViewBounds参数默认为false，当我们需要使用它的时候将其设置为true,其scaleType自动为“fitCenter”（当scaleType与adjustViewBounds同时在xml中设置后，如果设置了scaleType，则由adjustViewBounds自动设置的scaleType将失效，因为scaleType优先级比adjustViewBounds高），并且会根据当前View的最大宽高来填充View的内容，并且需要配合上maxHeight与maxWidth一起使用才会有效，因为其需要一个ImageView的界限

```
  <ImageView
        android:id="@+id/image"
        android:maxHeight="30dp"
        android:maxWidth="30dp"
        android:adjustViewBounds="true"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:src="@mipmap/icon_check"/>
```

---
- android:baseline

&emsp;相关方法:setBaseline(int)
&emsp;官方解释:视图中基线的偏移量。

&emsp;baseline默认为-1，此时基线处于ImageView的顶部，当通过setBaseLine设置偏移量为正数时代表视图的基线向下偏移，为负数时向上偏移，下面我们可以通过代码与图片的结合清楚的了解那条基线的位置(当baseline与baselineAlignBottom同时存在一个视图中时，基线以设置了baselineAlignBottom为主)
1. 设置偏移量为正数

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"
    >

    <TextView
        android:text="你好"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <ImageView
        android:baseline="50dp"
        android:id="@+id/image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"/>

</LinearLayout>
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190315142535145.png)

2. 设置偏移量为负数

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"
  >

    <TextView
        android:text="你好"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <ImageView
        android:baseline="-50dp"
        android:id="@+id/image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"/>

</LinearLayout>
```

![偏移量为-50dp](https://img-blog.csdnimg.cn/20190315141852422.png)
3. 不设置偏移量

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"
    >

    <TextView
        android:text="你好"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <ImageView
        android:id="@+id/image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"/>

</LinearLayout>
```

![无偏移量](https://img-blog.csdnimg.cn/20190315141622709.png)

---
- android:baselineAlignBottom
&emsp;相关方法:setBaselineAlignBottom(boolean)
&emsp;官方解释:如果为true，则图像视图将基于其底部边缘与基线对齐
&emsp;其实就是基线的位置处于ImageView的底部，展现出来的效果就是上图示例中我们的文字与图片底部对齐，其中baseline如果与baselineAlignBottom同时使用时，baseline设置得将毫无意义，基线位置以baselineAlignBottom设置的为主

```
    <LinearLayout
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
 
    <TextView
        android:text="你好"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <ImageView
        android:baselineAlignBottom="true"
        android:id="@+id/image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"/>
        
    </LinearLayout>
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190315143703505.png)

---
- android:cropToPadding
&emsp;相关方法:setCropToPadding(boolean)
&emsp;官方解释:如果为true，该图像将被裁剪以适合其填充
&emsp;这个参数如果设置为true，ImageView在onDraw的时候会根据scrollX、scrollY、padding等等参数来对图片进行裁剪，并将裁剪后的图片填充到界面上
![onDraw方法](https://img-blog.csdnimg.cn/20190315151529839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2E4MjM2NDM0MjY=,size_16,color_FFFFFF,t_70)

```
  <LinearLayout
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

    <TextView
        android:text="你好"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <ImageView
        android:paddingLeft="-20dp"
        android:cropToPadding="true"
        android:id="@+id/image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"/>

    </LinearLayout>
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190315151311937.png)

---
- android:maxHeight
&emsp;相关方法:setMaxHeight(int)
&emsp;官方解释:为视图提供最大的高度可选参数
该参数与maxWidth作用相似，都是相当于给ImageView设置一个最大的宽或高，该参数必须与android:adjustViewBounds配合使用，单独使用无效

---
- android:maxWidth
	&emsp;相关方法:	setMaxWidth(int)
&emsp;官方解释:为视图提供最大的宽度可选参数
该参数与maxWidth作用相似，都是相当于给ImageView设置一个最大的宽或高，该参数必须与android:adjustViewBounds配合使用，单独使用无效

---
- android:scaleType（重点掌握）
&emsp;相关方法:setScaleType(ImageView.caleType)
&emsp;官方解释:控制图像大小的调整或移动方式，以与此ImageView的大小匹配
Type:
1.  CENTER

&emsp;图片居中展示，如果图片大与控件大小，则以中心为基准展示ImageView控件大小，图片多出的部分裁剪不展示，如果图片小于控件大小，则全部展示，其余地方留白

2.  CENTER_CROP

&emsp;图片进行等比的缩放或拉伸，直到有宽或高有一方能够等于控件的宽高，多余的不展示

3.  CENTER_INSIDE

&emsp;将图片进行等比缩放，完整的展示在ImageView上，没有铺张到的地方显示背景色留白

4.  FIT_CENTER

&emsp;默认模式，图片将进行等比缩放或放大，完整的展示在ImageView上，并且没有铺张到的地方显示背景色。这里与CENTER_INSIDE有点类似，区别在于当同时都是小图片的时候，FIT_CENTER会在小图片的时候将图片拉伸至控件大小，而CENTER_INSIDE则只会居中显示，不拉伸

5. FIT_START

&emsp;图片进行等比缩放或放大，完整的展示在ImageView上，这一点和FIT_CENTER相似，不同点在于FIT_START是以左上为基准，当宽完整平铺展示并且高会有留白后，图片将在控件的上方，下方留白，如果高平铺展示，宽有留白时，则右边留白

6. FIT_END

&emsp;图片进行等比缩放或放大，完整展示在ImageView上，与FIT_START效果相反

7.  FIT_XY

&emsp;图片进行缩放或放大（非等比），显示在ImageView上，这里不是等比缩放或放大，会将图片完整的展示在ImageView上，一般来说图片宽高比和控件宽高比不一致的情况下，图片会呈现扭曲感

8.  MATRIX

&emsp;指定一种矩阵，因为其他七种都是内部已经写好了矩阵，这一种为自己指定一种矩阵，配合setImageMatrix()方法使用

快速记忆：其中CENTER,CENTER_CROP,CENTER_INSIDE共性：居中显示，FIT_CENTER,FIT_END,FIT_START,FIT_XY共性：对图片进行缩放，MATRIX指定矩阵

---
- android:src
	&emsp;相关方法:	setImageResource(int)
&emsp;官方解释:将可绘制的内容设置给ImageView

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190318221255862.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190318221404278.png)
---
- android:tint
&emsp;相关方法:setColorFilter(int,PorterDuff.Mode)
&emsp;官方解释:设置图像的颜色
这个方法翻译过来就是染色，它的作用就是改变图像每个像素的颜色，使用起来很简单，直接调用 setColorFilter();方法，传入ColorFilter，ColorFilter有三个派生子类LightingColorFilter，PorterDuffColorFilter，ColorMatrixColorFilter，这个方法虽然用起来很简单，但是其中却有很多细节，可以参考[Android中ImageView的ColorFilter图像处理解析](https://www.jianshu.com/p/bbc77334be95)，讲的很详细。
# 子类
## ImageButton
&emsp;ImageViewButton是一个类似Button的图像按钮
- 可以根据android:src或setimageresource（int）来为其设置图像
- 可以同时拥有前景和背景（前景在设置了固定的宽高后会出现图片失帧情况）
- 可以设置选择器，根据按钮的不同状态来改变按钮的图像等等（要选择生效必须控件获取到焦点）

```
    <ImageButton
        android:id="@+id/imageview"
        android:src="@drawable/bottom_line_style"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```
bottom_line_style.xml
```
 <?xml version="1.0" encoding="utf-8"?>
 <selector xmlns:android="http://schemas.android.com/apk/res/android">
     <item android:state_pressed="true"
           android:drawable="@drawable/button_pressed" /> <!-- pressed -->
     <item android:state_focused="true"
           android:drawable="@drawable/button_focused" /> <!-- focused -->
     <item android:drawable="@drawable/button_normal" /> <!-- default -->
 </selector>
```

## QuickContactBadge
&emsp;用于显示具有标准QuickContact徽章和单击行为的图像的小部件
该方法现在用的不多了，参考文章:[QuickContactBadge](https://www.cnblogs.com/over140/archive/2010/09/28/1837287.html)
## AppCompatImageView
&emsp;可以染色的ImageView，实现了TintableBackgroundView与TintableImageSourceView两个接口，用作对背景颜色进行动态改变，该ImageView在5.0以下的系统容易出现问题，较难排查，不建议使用

注：在高版本中还有一些其他的子类，但是不常见，所以没有例举


# 相关文章链接：
ImageView的ScaleType原理及效果分析 ：https://www.jianshu.com/p/fe5d2e3feed3
ImageView的ScaleType详解 ：https://www.cnblogs.com/pandapan/p/4614837.html
Android中ImageView的ColorFilter图像处理解析 ：https://www.jianshu.com/p/bbc77334be95
关于圆角ImageView的几种实现方式 ：https://www.jianshu.com/p/626dbd93207d

