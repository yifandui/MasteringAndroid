# RecyclerView 如何使用
> 文章来源：MasteringAndroid
>
> 作者：岳晓辉
>
> 审阅者：程序亦非猿
>
> 贡献者：




## `RecyclerView` 有哪些组件


### `LayoutManagers`
RecyclerView 需要布局管理器去管理view以哪一种方式展示给用户，内置以下三种布局管理器：

- `LinearLayoutManager`   横向或垂直方式显示
- `GridLayoutManager` 以卡片形式展示
- `StaggeredGridLayoutManager` 瀑布流效果
> 在使用RecyclerView的时候必须设置布局管理器

### `RecyclerView.Adapter`
`RecyclerView`的适配器，在使用的时候必须`override`俩个方法：一个是创建`ViewHolder`，另一个是将数据绑定到视图。

### `ItemAnimator`
如果你要给RecyclerView来一些动画效果就使用这个，例如删除和增加的动画效果。


## 基本的使用方式
1. 添加依赖库
2. 创建数据模型
3. 添加`RecyclerView`到主布局
4. 创建item的布局
5. 创建`RecyclerView.Adapter`和`ViewHolder`
6. RecyclerView绑定Adapter
  

### 添加依赖库
```gradle
dependencies {
    ...
    implementation 'com.android.support:recyclerview-v7:28.0.0'
}
```
### 创建数据模型
```java
package com.recyclerview.model;

public class User {

  private String name;
  private String age;

  public String getAge() {
    return age;
  }

  public void setAge(String age) {
    this.age = age;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

}

```
### 添加`RecyclerView`到主布局
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:tools="http://schemas.android.com/tools"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:orientation="vertical"
  tools:context=".MainActivity">

  <android.support.v7.widget.RecyclerView
    android:id="@+id/recycler_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />

</LinearLayout>
```
### 创建item的布局
位置`res/layout/item_user.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:tools="http://schemas.android.com/tools"
  android:layout_width="match_parent"
  android:layout_height="78dp"
  android:orientation="horizontal"
  android:padding="16dp">

  <TextView
    android:id="@+id/tv_name"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center_vertical"
    android:textColor="@color/colorPrimary"
    android:textSize="28sp"
    tools:text="名字" />

  <TextView
    android:id="@+id/tv_age"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:layout_gravity="center_vertical"
    android:layout_weight="1"
    android:gravity="right"
    android:textColor="@color/colorPrimary"
    android:textSize="28sp"
    tools:text="17" />

</LinearLayout>
```

### 创建`RecyclerView.Adapter`和`ViewHolder`
#### 创建Adapter
```java
package com.recyclerview;

import android.support.annotation.NonNull;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.LinearLayout;

import com.recyclerview.model.User;

import java.util.List;

public class UserAdapter extends RecyclerView.Adapter<UserViewHolder> {

  private List<User> userList;

  public UserAdapter() {
  }

  public void setData(List<User> userList) {
    this.userList = userList;
    notifyDataSetChanged();
  }

  @NonNull
  @Override
  public UserViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int position) {
    View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.item_user, viewGroup, false);
    return new UserViewHolder(view);
  }

  @Override
  public void onBindViewHolder(@NonNull UserViewHolder holder, int position) {
    User user = userList.get(position);
    holder.tvName.setText(user.getName());
    holder.tvAge.setText(user.getAge());
  }

  @Override
  public int getItemCount() {
    return null == userList ? 0 : userList.size();
  }

}

```


#### ViewHolder 创建
```java
package com.recyclerview;

import android.support.annotation.NonNull;
import android.support.v7.widget.RecyclerView;
import android.view.View;
import android.widget.TextView;

public class UserViewHolder extends RecyclerView.ViewHolder {

  public TextView tvName;
  public TextView tvAge;

  public UserViewHolder(@NonNull View itemView) {
    super(itemView);
    tvName = itemView.findViewById(R.id.tv_name);
    tvAge = itemView.findViewById(R.id.tv_age);
  }
}

```
### RecyclerView绑定Adapter
```java
package com.recyclerview;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.view.View;

import com.recyclerview.model.User;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    RecyclerView recyclerView = findViewById(R.id.recycler_view);

    //初始化数据
    List<User> userList = new ArrayList<User>();
    for (int i = 0; i < 10; i++) {
      User user = new User();
      user.setAge(String.valueOf(i));
      user.setName("士兵76：" + i);
      userList.add(user);
    }


    UserAdapter userAdapter = new UserAdapter();
    userAdapter.setData(userList);
    recyclerView.setAdapter(userAdapter);
    //设置布局管理器
    recyclerView.setLayoutManager(new LinearLayoutManager(this));
  }


}

```

### 单个条目修改

以前的数据
```java
List<User> userList = new ArrayList<User>();
    for (int i = 0; i < 10; i++) {
      User user = new User();
      user.setAge(String.valueOf(i));
      user.setName("士兵76：" + i);
      userList.add(user);
    }
```

新的数据,往0位置的上插入数据

```java
  User user = new User();
  user.setAge(String.valueOf(745));
  user.setName("插入一条数据：" + 745);
  userList.add(0, user);
  userAdapter.notifyItemInserted(0);
```

`notifyItemChanged(int pos)` 单个位置上发生改变
`notifyItemInserted(int pos)` 在某个位置上插入一条数据
`notifyItemRemoved(int pos)` 删除某个位置
`notifyDataSetChanged()` 数据发生改变的时候使用这个方法，通知adapter更新


## 其他的一些用法
### 添加分隔线
```java
 RecyclerView.ItemDecoration itemDecoration = 
            new DividerItemDecoration(this, DividerItemDecoration.VERTICAL);
    recyclerView.addItemDecoration(itemDecoration);
```
### 线性
```java
  LinearLayoutManager layoutManager = 
  new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false);
  recyclerView.setLayoutManager(layoutManager);
```

### 卡片
```java
  GridLayoutManager layoutManage = new GridLayoutManager(this, 2);
  recyclerView.setLayoutManager(layoutManage);
```

### 瀑布流
```java
    StaggeredGridLayoutManager gridLayoutManager =
            new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL);
    recyclerView.setLayoutManager(gridLayoutManager);
```

### 点击事件
```java
 public class UserViewHolder extends RecyclerView.ViewHolder {

    public TextView tvName;
    public TextView tvAge;

    public UserViewHolder(@NonNull View itemView) {
      super(itemView);
      tvName = itemView.findViewById(R.id.tv_name);
      tvAge = itemView.findViewById(R.id.tv_age);
      itemView.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
          if (listener != null) {
            int position = getAdapterPosition();
            if (position != RecyclerView.NO_POSITION) {
              listener.onItemClick(v, position);
            }
          }

        }
      });
    }

  }

  private OnClickListener listener;

  public interface OnClickListener {
    void onItemClick(View v, int position);
  }

  public void setOnClickListener(@Nullable OnClickListener listener) {
    this.listener = listener;
  }

```
使用点击事件
```java
  userAdapter.setOnClickListener(new UserAdapter.OnClickListener() {
      @Override
      public void onItemClick(View v, int position) {
        Toast.makeText(MainActivity.this, "点击:" + position, Toast.LENGTH_SHORT).show();
      }
    });
```



[源代码](https://github.com/ChinaVolvocars/RecyclerView)