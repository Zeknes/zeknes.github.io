---
layout: post
title: "安卓界面设置状态栏透明和图片延伸"
subtitle: "Transparent statusbar and image expand to statusbar"
author: "Eric"
header-style: text
tags:
  - Android(安卓)
  - UI
---



效果如图

![我的主页](/img/in-post/page-mine.jpg)



## 步骤



### 1. 在style.xml文件中添加如下代码

```xml
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- 其他设置 -->
        
        <!-- 隐藏标题栏和设置状态栏透明 -->
        <item name="windowActionBar">false</item>
        <item name="windowNoTitle">true</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowDrawsSystemBarBackgrounds">true</item>
        <item name="android:statusBarColor">@android:color/transparent</item>
    </style>
```



### 2. 在xml中删除属性

```xml
android:fitsSystemWindows="true"
```

