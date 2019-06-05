

> 文章来源：MasteringAndroid
>
> 作者：Stig.Li
>
> 审阅者：程序亦非猿
>
> 贡献者：



### 知识点简介

Material Design于Google I/O 2014 发布,是一套适用于 Android Mobile、Android Table、Desktop Chrome 等全平台设计语言规范,在Android 5.0（API级别21）中引入(以下简称为MD)。

相对于iOS的扁平化风格来说,新出的MD设计规范在界面风格上挽回不少颜面。UI设计师偏爱的设计风格为iOS扁平风，一套设计模板共用 Android 和 iOS，应该是众多中小型公司的通用策略。但是在iOS9以后的iOS系统中出现很多借鉴MD设计风格的控件，足矣说明MD设计的魅力所在。

在Android5.0之前，用Android原生系统控件做出来的应用可以说是不忍直视，Android5.0之后，谷歌提供MD设计风格Design Support Library控件库，大大的提高了应用美观程度，而且支持Android 2.1以上设备。至此以后整个Android生态环境中MD风格的APP越来越多，美观度方面丝毫不逊色于iOS的应用风格。

当Android开发者需要开发一款APP而苦于没有UI设计的时候，我们完全可以用Design Support Library控件库去实现。

最主要的就是能节省大量开发时间而又能保证界面美观性。

本文章主要讲解的是Material Design for Android常用组件的基本使用。


### 基本的使用方式

Design Support库的使用
基于当前最新的compileSdkVersion 28,在工程目录下build.gradl文件中添加如下
`implementation 'com.android.support:design:28.0.0'`


这是Design Support Library包下思维导图。
![Design_Support_Library.svg](./assets/Design_Support_Library.svg)



### 需要掌握的知识点

以下为**Design Support Library**包下需要掌握使用的控件

1. **TextInputLayout**	 

	可简单理解为**EditText**辅助控件,**TextInputLayout**布局用法,可兼容**EditText**和**TextInputEditText**组件，**hint**可以设置在**TextInputLayout**内，也可以设置在**EditText**和**TextInputEditText**内，效果一样
	
	```
	<android.support.design.widget.TextInputLayout
        android:id="@+id/layout_account"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="60dp"
        android:layout_marginRight="20dp"
        android:layout_marginBottom="20dp">

        <EditText
            android:id="@+id/et_username"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="用户名" />

    </android.support.design.widget.TextInputLayout>
   
	```
	
	```
	 <android.support.design.widget.TextInputLayout
        android:id="@+id/layout_phone"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dp"
        android:layout_marginRight="20dp"
        android:layout_marginBottom="20dp"
        android:hint="电话号码">

        <android.support.design.widget.TextInputEditText
            android:id="@+id/tiet_phone"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="number" />

    </android.support.design.widget.TextInputLayout>
	```
	运用以上布局代码就可以实现以下效果
	
	![md_textinputlayout_hint.png](./assets/md_textinputlayout_hint.png)
	
	**TextInputLayout**还有一个错误提示属性,以下为使用方式
	
		String username = etUsername.getText().toString().trim();
  	  if (!TextUtils.isEmpty(username) && username.length() < 16) {
  	       layoutAccount.setError("用户名符合要求");
  	       layoutAccount.setErrorEnabled(true);
  	       layoutAccount.setErrorTextColor(ColorStateList.valueOf(Color.GREEN));
  	   } else {
  	       layoutAccount.setError("用户名不符合要求");
  	       layoutAccount.setErrorEnabled(true);
  	       layoutAccount.setErrorTextColor(ColorStateList.valueOf(Color.RED));
  	   }
  	  String pwd = etPwd.getText().toString().trim();
  	   if (!TextUtils.isEmpty(pwd) && pwd.length() >= 6 && pwd.length() < 12) {
  	       layoutPassword.setError("密码符合要求");
  	       layoutPassword.setErrorEnabled(true);
  	       layoutPassword.setErrorTextColor(ColorStateList.valueOf(Color.GREEN));
  	    } else {
  	       layoutPassword.setError("密码不符合要求");
  	       layoutPassword.setErrorEnabled(true);
  	       layoutPassword.setErrorTextColor(ColorStateList.valueOf(Color.RED));
  	    }
  	    String tel = Objects.requireNonNull(tietPhone.getText()).toString().trim();
  	    if (tel.length() > 0 && tel.length() == 11) {
  	       layoutPhone.setError("电话号码符合要求");
  	       layoutPhone.setErrorEnabled(true);
  	       layoutPhone.setErrorTextColor(ColorStateList.valueOf(Color.GREEN));
  	     } else {
  	       layoutPhone.setError("电话号码不符合要求");
  	       layoutPhone.setErrorEnabled(true);
  	       layoutPhone.setErrorTextColor(ColorStateList.valueOf(Color.RED));
  	     }		
  	     
  	运用以上布局代码实现的效果如下图
	![md_textinputlayout_error.png](./assets/md_textinputlayout_error.png)
	


2. **FloatingActionButton**	
	
	基本可以理解为MD风格的圆形按钮,**FloatingActionButton**悬浮按钮控件(以下简称FAB)。从源码查看得知，FAB是继承至ImageView，所以FAB拥有ImageView的全部属性。FAB的精髓在于与**CoordinatorLayout**协调者布局中的使用。我们先看看常用FAB自定义域名空	间属性说明。
	
	```
	backgroundTint - 设置FAB的背景颜色。
	app:rippleColor - 设置FAB点击时的背景颜色。
	app:borderWidth - 该属性尤为重要。假设不设置0dp。那么在4.1的sdk上FAB会显示为正方形。
	并且在5.0以后的sdk没有阴影效果。所以设置为borderWidth="0dp"。
	app:elevation - 默认状态下FAB的阴影大小。
	app:pressedTranslationZ - 点击时候FAB的阴影大小。
	app:fabSize - 设置FAB的大小，该属性有3个值，分别为normal和mini，相应的FAB大小分别为56dp和40dp。最后一	个为auto
	src - 设置FAB的图标，Google建议符合Design设计的该图标大小为24dp。
	app:layout_anchor - 设置FAB的锚点，即以哪个控件为參照点设置位置。
	app:layout_anchorGravity - 设置FAB相对锚点的位置，值有 bottom、center、right、left、top等。
	```
	
	xml布局代码如下
	
	```
	 <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="20dp"
        android:src="@mipmap/ic_launcher_round"
        app:backgroundTint="@android:color/holo_blue_dark"
        app:borderWidth="0dp"
        app:elevation="5dp"
        app:fabSize="auto"
        app:layout_anchorGravity="right|bottom"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:pressedTranslationZ="10dp"
        app:rippleColor="@android:color/darker_gray" />
	```
	FAB控件显示效果图
	
	![md-floatingfctionbutton.png](./assets/md-floatingfctionbutton.png)



3. **Snackbar**
	**Snackbar**是一个提示框组件，使用Snackbar会在屏幕底部显示一条消息，与Toast相似。
	基础使用方法如下:
	
	
	```
	Snackbar.make(view, "确认收藏 FloatingActionButton 吗？",Snackbar.LENGTH_SHORT)
			.setAction("取消", new View.OnClickListener() {
                    @Override
                    public void onClick(View view) {
                        //TODO 响应点击事件
                    }
                }).show();
	```
	
	效果图
	
	![md-snackbar.png](./assets/md-snackbar.png)


4. **TabLayout**
  
   **TabLayout**应该搭配**TabItem**一起使用,作为选项卡组件使用，同时中间选项卡容器可搭配**ViewPager**等容器使用。
   基础使用方法如下
   
   ```
     <android.support.design.widget.TabLayout
        android:id="@+id/tabs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toTopOf="parent"
        app:tabIndicatorColor="@android:color/holo_blue_dark"
        app:tabIndicatorHeight="1dp"
        app:tabSelectedTextColor="@android:color/holo_orange_light">

        <android.support.design.widget.TabItem
            android:id="@+id/tabitem_home"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:icon="@mipmap/ic_launcher"
            android:text="主页" />

        <android.support.design.widget.TabItem
            android:id="@+id/tabitem_recommend"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:icon="@mipmap/ic_launcher"
            android:text="推荐" />

        <android.support.design.widget.TabItem
            android:id="@+id/tabitem_mine"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:icon="@mipmap/ic_launcher"
            android:text="我的" />
    </android.support.design.widget.TabLayout>
   
   ```
   
   效果图如下
   	![md-tablayout.png](./assets/md-tablayout.png)
   
5. **NavigationView**
	
	**NavigationView**是用于菜单导航的View, **headerLayout**属性代表头部布局，menu属性则为**NavigationView**组件item选项, **NavigationView**组件多用于**DrawerLayout**布局一起使用，也就是我们常说的侧拉菜单控件。
	
	以下为xml布局常用方式，**headerLayout**为普通布局，**menu**为菜单选项，新建**menu**文件并引用即可。
	
	```
	  <android.support.design.widget.NavigationView
        android:id="@+id/navigation_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="left"
        app:headerLayout="@layout/layout_navigation_haeder"
        app:menu="@menu/main" />
        
	```
	
	简单图示效果
	
	![md_navigationiview.png](./assets/md_navigationiview.png)


6. **CoordinatorLayout**	
	
	**CoordinatorLayout**又名协调者布局。简**CoordinatorLayout**是用来协调其子view并以触摸影响布局的形式产生动画效果的一个super-powered FrameLayout，其典型的子View包括：FloatingActionButton，SnackBar。注意：**CoordinatorLayout**是一个顶级父View,可以称之为超级**FrameLayout**。
	
	我们用**CoordinatorLayout**布局来解决一下在Snackbar演示中遇到的点击**FloatingActionButton**弹出提示被遮盖问题,我们来看下xml布局:
	
	```
	<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/contentView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:id="@+id/layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical" />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginRight="10dp"
        android:layout_marginBottom="10dp"
        android:onClick="onClick"
        android:src="@mipmap/ic_launcher"
        app:layout_anchor="@id/layout"
        app:layout_anchorGravity="bottom|right" />
</android.support.design.widget.CoordinatorLayout>
	```
	
	效果图
	![md-coordinatorlayout.png](./assets/md-coordinatorlayout.png)
	
	是不是完美解决了**Snackbar**演示中的问题，当然**CoordinatorLayout**还有更多的适用场景，这只是其中一个。



7. **AppBarLayout**	
	
	**AppBarLayout**继承自LinearLayout，子控件默认为竖直方向显示，可以用它实现Material Design 的Toolbar；它支持滑动手势；它的子控件可以通过在代码里调用setScrollFlags(int)或者在xml里app:layout_scrollFlags来设置它的滑动手势(5种滚动标识)。当然实现这些的前提是它的根布局必须是 **CoordinatorLayout**。这里的滑动手势可以理解为：当某个可滚动View的滚动手势发生变化时，**AppBarLayout**内部的子View实现某种动作。AppBarLayout的子控件不仅仅可以设置为Toolbar，也可以包含其他的View。
	
	作为MD风格的滑动Layout，我们将结合**CollapsingToolbarLayout组件**一起演示。

8. **CollapsingToolbarLayout**
	
	**CollapsingToolbarLayout**作用是提供了一个可以折叠的**Toolbar**，它继承自**FrameLayout**，给它设置layout_scrollFlags，它可以控制包含在**CollapsingToolbarLayout**控件(如：ImageView、Toolbar)在响应layout_behavior事件时作出相应的scrollFlags滚动事件(移除屏幕或固定在屏幕顶端)。CollapsingToolbarLayout可以通过app:contentScrim设置折叠时工具栏布局的颜色，通过app:statusBarScrim设置折叠时状态栏的颜色。


	AppBarLayout是一种支持响应滚动手势的app bar布局（比如工具栏滚出或滚入屏幕），CollapsingToolbarLayout则是专门用来实现子布局内不同元素响应滚动细节的布局。与AppBarLayout组合的滚动布局（Recyclerview、NestedScrollView等）需要设置app:layout_behavior这个属性。没有设置的话，AppBarLayout将不会响应滚动布局的滚动事件。
	
	注意:CollapsingToolbarLayout和ScrollView一起使用会有滑动bug，注意要使用NestedScrollView来替代ScrollView。
	
	**CollapsingToolbarLayout**可以通过app:contentScrim设置折叠时工具栏布局的颜色，通过app:statusBarScrim设置折叠时状态栏的颜色。默认contentScrim是colorPrimary的色值，statusBarScrim是colorPrimaryDark的色值。
	
	**CollapsingToolbarLayout**的子布局有3种折叠模式（Toolbar中设置的app:layout_collapseMode）
	
		off：这个是默认属性，布局将正常显示，没有折叠的行为。
		pin：CollapsingToolbarLayout折叠后，此布局将固定在顶部。
		parallax：CollapsingToolbarLayout折叠时，此布局也会有视差折叠效果。
当CollapsingToolbarLayout的子布局设置了parallax模式时，我们还可以通过app:layout_collapseParallaxMultiplier设置视差滚动因子，值为：0~1。

	那我们将利用**AppBarLayout**和**CollapsingToolbarLayout**来实现一个ScrollingActivity，以下为xml布局代码
	
	```
	<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
	xmlns:app="http://schemas.android.com/apk/res-auto"
	xmlns:tools="http://schemas.android.com/tools"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:fitsSystemWindows="true"
	tools:context=".ScrollingActivity">
	
	<android.support.design.widget.AppBarLayout
	    android:id="@+id/app_bar"
	    android:layout_width="match_parent"
	    android:layout_height="@dimen/app_bar_height"
	    android:fitsSystemWindows="true"
	    android:theme="@style/AppTheme.AppBarOverlay">
	
	    <android.support.design.widget.CollapsingToolbarLayout
	        android:id="@+id/toolbar_layout"
	        android:layout_width="match_parent"
	        android:layout_height="match_parent"
	        android:fitsSystemWindows="true"
	        app:contentScrim="?attr/colorPrimary"
	        app:layout_scrollFlags="scroll|exitUntilCollapsed"
	        app:toolbarId="@+id/toolbar">
	
	        <android.support.v7.widget.Toolbar
	            android:id="@+id/toolbar"
	            android:layout_width="match_parent"
	            android:layout_height="?attr/actionBarSize"
	            app:layout_collapseMode="pin"
	            app:popupTheme="@style/AppTheme.PopupOverlay" />
	
	    </android.support.design.widget.CollapsingToolbarLayout>
	</android.support.design.widget.AppBarLayout>
	
	<android.support.v4.widget.NestedScrollView
	    xmlns:app="http://schemas.android.com/apk/res-auto"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    app:layout_behavior="@string/appbar_scrolling_view_behavior">
	
	    <TextView
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:layout_margin="@dimen/text_margin"
	        android:text="@string/large_text" />
	
	</android.support.v4.widget.NestedScrollView>
	
	<android.support.design.widget.FloatingActionButton
	    android:id="@+id/fab"
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content"
	    android:layout_margin="@dimen/fab_margin"
	    app:layout_anchor="@id/app_bar"
	    app:layout_anchorGravity="bottom|end"
	    app:srcCompat="@android:drawable/ic_dialog_email" />
	
	```
	记得要在Activity中加上这句代码哦
	
	```
	Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
	setSupportActionBar(toolbar);
	```
	以上就是利用**AppBarLayout**和**CollapsingToolbarLayout**实现的Scrolling效果，还可以设置AppBarLayout的滑动监听、修改透明度和颜色变化等，有兴趣可以研究下用AppBarLayout和CollapsingToolbarLayout实现更棒的效果。
	
	![md_scrolling.gif](./assets/md_scrolling.gif)

9. **MaterialButton**

	**MaterialButton**是Material风格的Button,使用时需要同时设置android:textAllCaps和android: textAppearance,增加了**MaterialButton**对圆角、线宽、图片颜色、图片尺寸等方式的，其余使用方法与原生Botton基本一样。总之大大方便我们对Button的使用。
	
	```
	<android.support.design.button.MaterialButton
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:layout_marginLeft="30dp"
        android:layout_marginRight="30dp"
        android:text="Material Button"
        android:textAllCaps="false"
        android:textAppearance="?android:attr/textAppearanceLargeInverse"
        app:icon="@android:drawable/ic_popup_reminder"
        android:paddingBottom="10dp"
        android:paddingTop="10dp"
        android:paddingLeft="10dp"
        android:clickable="true"
        android:paddingRight="10dp"
        app:iconSize="30dp"
        app:iconTint="@color/colorPrimary"
        app:strokeColor="@color/colorPrimaryDark"
        app:strokeWidth="2dp" />	

	```
	
	效果图:
	![md-materialbutton.png](./assets/md-materialbutton.png)
  
10. **Chip** & **ChipGroup**

	Chip的用法和分类:
	
	**Action chip**
		
	使用 style="@style/Widget.MaterialComponents.Chip.Action"
不设置style时，默认使用上述style默认前后图标都不展示，点击后没有选中状态
	
	**Filter Chip**
	
	使用 style="@style/Widget.MaterialComponents.Chip.Filter"
初始状态下， 不展示前后图标点击之后会展示前面的选中图标，并且具有选中状态
通常应用在 ChipGroup 中

	**Entry Chip**
	
	 使用style="@style/Widget.MaterialComponents.Chip.Entry"
默认在末尾展示删除按钮；点击后前面展示选中图标，有选中状态通常可以作为 chipDrawable 使用，比如在填选邮件收件人时可以使用

	**Choice Chip**

	默认不展示前后的图标，但点击后有选中状态
	通常用在 ChipGroup 中 , 通过 ChipGroup 的 singleSelection=true/false 属性可以实现单选或多选
	
	代码块
	
	```
	 <android.support.design.chip.Chip
            android:id="@+id/chip1"
            style="@style/Widget.MaterialComponents.Chip.Action"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:clickable="true"
            android:text="ChipAction"
            android:textAllCaps="false"
            android:textAppearance="?android:textAppearance" />

        <android.support.design.chip.Chip
            android:id="@+id/chip2"
            style="@style/Widget.MaterialComponents.Chip.Filter"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:clickable="true"
            android:text="ChipFilter"
            android:textAllCaps="false"
            android:textAppearance="?android:textAppearance"/>

        <android.support.design.chip.Chip
            android:id="@+id/chip4"
            style="@style/Widget.MaterialComponents.Chip.Entry"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:clickable="true"
            android:text="ChipEntry"
            android:textAllCaps="false"
            android:textAppearance="?android:textAppearance" />

        <android.support.design.chip.Chip
            android:id="@+id/chip3"
            style="@style/Widget.MaterialComponents.Chip.Choice"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:clickable="true"
            android:text="ChipChoice"
            android:textAllCaps="false"
            android:textAppearance="?android:textAppearance" />
	```
	
	效果图
	![md-chip.gif](./assets/md-chip.gif)
	
	**Chip**继承至**AppCompatCheckBox**组件，基础用法可参考AppCompatCheckBox,常用事件监听有OnClickListener和OnCheckedChangeListener,Chip基础用法基本就到此为止，还有更多自定义空间属性请大家自行查阅。
	
	**ChipGroup**
	用法与**RadioGroup**类似,ChipGroup是用来管理多个Chip的,可以控制多个Chip的布局方式以及事件。不同的是ChipGroup继承至FlowLayout，RadioGroup继承至LinearLayout.使用ChipGroup可以方便的实现流式布局效果。其特点如下： 
	* 默认情况下， **ChipGroup**中的**chip**会横向排列，当超过一行时会执行换行操作。 
	* 如果我们不想让 Chip 换行，那么为 ChipGroup 设置 app:singleLine=true，如果 Chip 会超过一行，则在外层包裹 HorizontalScrollView 
	* 只有当其中包裹的 Chip 是 checkable=true 时，才具有选中效果

	效果一
	![md-chipgroup1.gif](./assets/md-chipgroup1.gif)
	
	代码块
	
	```
	 <!--ChipGroup 默认状态，会换行，可多选-->
    <android.support.design.chip.ChipGroup
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        app:chipSpacing="100dp"
        app:chipSpacingHorizontal="35dp"
        app:chipSpacingVertical="10dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <android.support.design.chip.Chip
            android:id="@+id/chip1"
            style="@style/Widget.MaterialComponents.Chip.Filter"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingLeft="50dp"
            android:paddingRight="50dp"
            android:text="Chip1"
            android:textAllCaps="false"
            android:textAppearance="?android:textAppearance" />

        <android.support.design.chip.Chip
            android:id="@+id/chip2"
            style="@style/Widget.MaterialComponents.Chip.Filter"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingLeft="80dp"
            android:paddingRight="80dp"
            android:text="Chip2"
            android:textAllCaps="false"
            android:textAppearance="?android:textAppearance" />

        <android.support.design.chip.Chip
            android:id="@+id/chipInGroup3"
            style="@style/Widget.MaterialComponents.Chip.Filter"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingLeft="80dp"
            android:paddingRight="80dp"
            android:text="Chip3"
            android:textAllCaps="false"
            android:textAppearance="?android:textAppearance" />

    </android.support.design.chip.ChipGroup>
	```
	效果二
	
	![md-chipgroup2.gif](./assets/md-chipgroup2.gif)
	
	代码块
	
	```
	  <!--ChipGroup 不换行，单选-->
    <HorizontalScrollView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:scrollbars="none">

        <android.support.design.chip.ChipGroup
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            app:checkedChip="@id/chip1"
            app:chipSpacing="25dp"
            app:singleLine="true"
            app:singleSelection="true">

            <android.support.design.chip.Chip
                android:id="@+id/chip1"
                style="@style/Widget.MaterialComponents.Chip.Filter"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Chip1"
                android:textAllCaps="false"
                android:textAppearance="?android:textAppearance" />

            <android.support.design.chip.Chip
                android:id="@+id/chip2"
                style="@style/Widget.MaterialComponents.Chip.Filter"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Chip2"
                android:textAllCaps="false"
                android:textAppearance="?android:textAppearance" />

            <android.support.design.chip.Chip
                android:id="@+id/chip3"
                style="@style/Widget.MaterialComponents.Chip.Filter"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Chip3"
                android:textAllCaps="false"
                android:textAppearance="?android:textAppearance" />

            <android.support.design.chip.Chip
                android:id="@+id/chip5"
                style="@style/Widget.MaterialComponents.Chip.Filter"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Chip5"
                android:textAllCaps="false"
                android:textAppearance="?android:textAppearance" />

            <android.support.design.chip.Chip
                android:id="@+id/chip4"
                style="@style/Widget.MaterialComponents.Chip.Filter"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Chip4"
                android:textAllCaps="false"
                android:textAppearance="?android:textAppearance" />

            <android.support.design.chip.Chip
                android:id="@+id/chip6"
                style="@style/Widget.MaterialComponents.Chip.Filter"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Chip6"
                android:textAllCaps="false"
                android:textAppearance="?android:textAppearance" />

        </android.support.design.chip.ChipGroup>
    </HorizontalScrollView>
	```
	
11. **MaterialCardView**
	**MaterialCardView**继承至**CardView**，基础用法基本跟CardView一致，新增 app:strokeColor、app:strokeWidth两个属性，主要用来控制边框线宽和颜色。MaterialCardView可搭配RecycleView等一起使用。MaterialCardView的特性主要有边框圆角效果、阴影效果、Ripple水波纹效果等,还可以自定义属性动画。
	
	简单xml布局代码
	
	```
	 <android.support.design.card.MaterialCardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:clickable="true"
        app:cardUseCompatPadding="true"
        android:foreground="@color/colorPrimary"
        app:cardBackgroundColor="@color/colorPrimary"
        app:cardCornerRadius="5dp">

        <ImageView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:src="@mipmap/ic_launcher" />
  ```


    </android.support.design.card.MaterialCardView>
    ```
    效果图
    ![md_materialcardview.png](./assets/md_materialcardview.png)



### 学习路线以及阅读的资料推荐
建议先从基础组件的使用入手，然后再通过实现基础的UI效果，逐步熟悉各个组件之间的关系。以及熟练运用各个组件实现更为复杂的UI效果。（PS:再好的理论知识也不如自己敲一手代码来的实在）

以下为阅读资料推荐:

> Android developer:https://developer.android.google.cn/guide/topics/ui/look-and-feel
> 
> Material design:https://material.io
> 
> 极客学院:http://wiki.jikexueyuan.com/project/material-design/material-design-intro/introduction.html
> 
>1sters 社区:http://design.1sters.com/material_design/material-design/introduction.html
>
>github:https://github.com/material-components/material-components-android


