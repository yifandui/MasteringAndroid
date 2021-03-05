# MasteringAndroid



MasteringAndroid 的愿景是：

「帮助每一个 Android 开发者更加快速、更加全面以及更加深入地学习并掌握 Android 。」

(trying)

### 学习 Android 的困境



相信每个 Android 开发者都或多或少的遇到过一些学习以及成长上面的难题。

1. 想了解 Android 但是不知道从哪里开始学习？
2. 想学习 Android 但是不知道应该学习一些什么东西？
2. 


该项目会帮助你：

1. 会告诉你学习 Android 开发究竟应该学些什么；
2. 每个



UI TODO:

- ConstraintLayout ： 余承润

- [RecyclerView](./android/ui/recyclerview.md)

- ViewPager

- [TextView](./android/ui/textview.md) 

- [ImageView](./android/ui/ImageView.md)  高祥

- MaterialDesign 李仕庭

- [UI 性能优化](./android/ui/UI性能优化.md) 

- 自定义 View

- Drawable   管鹏杰

  [第一部分](./android/ui/Android-Drawable-Use-Part1.md)
  [第二部分](./android/ui/Android-Drawable-Use-Part2.md)

- Toast

- PopupWindow

- DialogFragment ： 宋纪琛

- UI 适配

- FlexboxLayout

- Fragment ： 李腾

- Animation ： 徐晓涵

- Bitmap  ：江清锋

- ViewRootImpl : 何昌辉



JetPack:

1. Lifecycle
2. LiveData
3. ViewModel
4. Paging
5. Room
6. ...



Third Party :

1. Retrofit
2. OkHttp  吴文彬
3. EventBus 何昌辉
4. Glide
5. LeakCanary
6. ...

### Android 适配


适配 Android Q ：


hidden api :

https://developer.android.google.cn/distribute/best-practices/develop/restrictions-non-sdk-interfaces#test-for-non-sdk
veridex：https://android.googlesource.com/platform/prebuilts/runtime/+/master/appcompat

 ./appcompat.sh --dex-file=你的apk的路径

### 规范



#### 资源规范

资源存放到 Android 的 android/assets 目录



命名规则： `知识点-描述` 

比如 textview-spannable







