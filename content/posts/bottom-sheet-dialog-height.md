---
title: 如何设置 BottomSheetDialog 高度
date: 2017-02-18 23:45:23
tags: [Android]
categories: Android
draft: false
---

BottomSheetDialog 控制初始高度是跟 behavior_peekHeight 属性有关

### 样式主题设置
```
<style name="BottomSheetDialog" parent="Theme.Design.Light.BottomSheetDialog">
    <item name="bottomSheetStyle">@style/bottomSheetStyleWrapper</item>
</style>

<style name="bottomSheetStyleWrapper" parent="Widget.Design.BottomSheet.Modal">
    <item name="behavior_peekHeight">500dp</item>
</style>

<!--代码中应用-->
BottomSheetDialog dialog = new BottomSheetDialog(this, R.style.BottomSheetDialog);
            dialog.setContentView(R.layout.layout_bottom_sheet);
            dialog.show();
```

### 动态代码设置

1. Dialog 的布局最外层使用 RelativeLayout 或者 ConstraintLayout, 其他控件设置高度无法生效
2. 需要在子内容布局设置高度 match_parent；
```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/rl_container"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@color/text_white">

    <LinearLayout
        android:id="@+id/ll_address_top"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:minHeight="?attr/actionBarSize"
        android:orientation="horizontal">
        
    </LinearLayout>

</RelativeLayout>

```
3. 设置高度的代码

```
View parent = (View) mContentView.getParent();
if (parent != null) {
    BottomSheetBehavior mBehavior = BottomSheetBehavior.from(parent);
    DisplayMetrics displayMetrics = getContext().getResources().getDisplayMetrics();
    int screenHeight = displayMetrics.heightPixels;
    mBehavior.setPeekHeight((int) (screenHeight * 0.45));
    parent.setBackgroundColor(Color.TRANSPARENT);
}
```

### 简单粗暴直接给父布局一个高度

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/rl_container"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="300dp"
    android:background="@color/text_white">

    <LinearLayout
        android:id="@+id/ll_address_top"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:minHeight="?attr/actionBarSize"
        android:orientation="horizontal">
        
    </LinearLayout>

</RelativeLayout>
```