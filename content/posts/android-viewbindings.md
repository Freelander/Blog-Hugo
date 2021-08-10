---
title: 是时候让你项目用上 ViewBindings 了
date: 2019-06-13 23:37:07
tags: [Android,DataBinding,ViewBindings]
categories: Android
draft: false
---

## 写在前面

相信大多数人在写 findViewById 都是烦恼的，不少人会使用 ButterKnife 来简化，利用插件会自动生成绑定 View 代码，可是一旦界面控件多时候，就会出现几十行甚至上百行的 BindView 代码。此时有没有更好的框架来帮我们呢？

答案是有的，在前不久的 Google IO 2019 上面提到了 ViewBindings 概念，后来我详细了解后，发现这个东西可以理解为 DataBinding 的一部分，具体区别在于 ViewBindings 只是视图绑定部分。想更深入了解两者区别，可以看下 [这篇文章](https://proandroiddev.com/new-in-android-viewbindings-the-difference-from-databinding-library-bef5945baf5e)

## 使用

### 构建 DataBingding 环境

在 App 模块的 build.gradle 文件中添加 DataBinding 配置

```
android {
    ....
    dataBinding {
        enabled = true
    }
}
```

### 在基类配置 Bingding 代码

#### Activity

```
public class CustomActivity<VB extends ViewDataBinding> extends AppCompatActivity {

    private View mContentView;
    protected VB mViewBinding;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }
    
    @Override
    public void setContentView(@LayoutRes int layoutResID) {
        mContentView = View.inflate(this, layoutResID, null);
        mViewBinding = DataBindingUtil.bind(mContentView);
        super.setContentView(mContentView);
    }
}
```
#### Fragment

```
public class CustomFragment<VB extends ViewDataBinding> extends BaseFragment {
    
    private View mContentView;
    protected VB mViewBinding;
    
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        return mContentView;
    }
    
    public void setContentView(int id) {
        this.mContentView = getActivity().getLayoutInflater().inflate(id, mContainer, false);
        mViewBinding = DataBindingUtil.bind(mContentView);
    }
}
```
### 如何使用

#### 布局

在布局的最外层增加 ``<layout></layout>`` 标签

```
<?xml version="1.0" encoding="utf-8"?>
<layout>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/bg_grey_f2f2f2"
        android:orientation="vertical">

        <com.chicdeals.widget.CustomRegularTextView
            android:id="@+id/tv_app_cache"
            style="@style/CommonRippleStyle"
            android:layout_width="match_parent"
            android:layout_height="@dimen/space_50"
            android:drawableEnd="@mipmap/ic_account_arrow_default"
            android:drawablePadding="@dimen/space_12"
            android:gravity="center_vertical"
            android:paddingStart="@dimen/space_12"
            android:paddingEnd="@dimen/space_12"
            android:text="@string/account_change_password"
            android:textColor="@color/text_grey_333333"
            android:textSize="@dimen/textSize_15" />

    </LinearLayout>
</layout>
```
#### 代码

```
public class SettingActivity extends CustomActivity<SettingActivityBinding> {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.setting_activity);
    }
    
    @Override
    public void initView() {
        super.initView();
        
        mViewBinding.tvAppCache.setText(getCacheSize());
    }
}
```

### 视图点击事件

在引入 DataBinding 时，发现如果处理视图点击，还是需要逐一给视图设置点击事件的绑定，没有其他更好的方法，此时我选择了继续保留 ButterKnife 框架，可以快速绑定视图的点击事件

```
@OnClick({R.id.tv_change_pwd, R.id.tv_app_version, R.id.tv_app_cache, R.id.cv_sign_out})
public void onViewClicked(View view) {
    switch (view.getId()) {
        case R.id.tv_change_pwd:
            break;
        case R.id.tv_app_version:
            break;
        case R.id.tv_app_cache:
            break;
        case R.id.cv_sign_out:
            break;
    }
}
```

## 结束

到这里简述大概如何使用 DataBinding 中的 ViewBinding 部分了。此时有人疑惑为什么不说说 DataBinding 数据绑定部分？

我简单说下几个原因：

1. DataBinding 将部分逻辑移到布局文件，使得布局失去纯粹性，且逻辑变得有点乱，不利于阅读代码；
2. 如果某个布局 xml 中的 DataBinding 代码有误，编译时会全部布局文件报错，不好定位报错地方；
3. 在布局文件中插入了部分数据逻辑，使得文件失去了复用性。

综上所述，我只建议大家使用 DataBinding 中的 View 绑定模块的 ViewBindings 框架，解决一个小小需求，替代 findViewById 使得项目代码更加简洁。


