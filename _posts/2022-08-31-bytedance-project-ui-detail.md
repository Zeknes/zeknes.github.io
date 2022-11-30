---
layout: post
title: "字节跳动安卓项目ui部分说明"
subtitle: "Bytedance project ui detail"
author: "Eric"
header-style: text
tags:
  - Android(安卓)
  - xml
  - UI
  - Layout
  - Glide
---





`系统架构`，`retrofit`+`rxjava`网络，`room数据库`等在小组报告中已有详细说明，就不再赘述，本文主要叙述小组报告中遗漏的个人部分，如界面和换背景功能等.



项目地址: [Github](https://github.com/eric0202/DouYin)



## 概览



主要功能界面截图:

![screenshots](/img/in-post/bytedance-android-app-screenshot.png)



视频演示:

<html>

<video width="300" height="700" controls>
    <source src="/img/in-post/bytedance-app-video-demo.mp4" type="video/mp4">
</video>
</html>



## 功能



### 1. 个人界面以及界面相应功能



**功能简述**

界面的设计为尽量的接近青训营提供的截图界面。主要设计点如下：

1：图片设计

2：布局控件设计

3：右上角菜单设计



#### 1. 图片设计



​	图片主要有三种：

​	图标 - 简单的src加载实现
```java
android:id="@+id/img_store" 
```



​	头像 - 通过`glide`加载`CropCircleWithBorderTransformation`参数，实现带白边圆形图片

```java
RequestOptions options = RequestOptions.bitmapTransform(new CropCircleWithBorderTransformation(3, Color.WHITE));
    Glide.with(this).load(R.drawable.r_key).apply(options).into(binding.imgAvatar);
```



​	背景图 - 通过`glide`加载`BlurTransformation`参数，实现高斯模糊 (后期觉得去掉模糊效果可能更适合展示，没有加上

```java
    RequestOptions options1 = RequestOptions.bitmapTransform(new BlurTransformation(50,1));
    Glide.with(requireContext()).load(UriUtil.convertUriToPath(getContext(),background)).apply(options1).into(binding.imgWall);
```





#### 2. 布局控件设计



<html>

<img src="/img/in-post/bytedance-app-blueprint.png" width="280" height="400"/>

</html>



##### 1. 布局设计

​	根据布局层级有不同的布局设计

​	最外层设计 - 由于`《我的》`页面有明显上下分层，于是采用了`vertical LinearLayout`的方式
背景头像层设计 - 由于文字和头像在被背景图片上，且文字比较多，采用了`constraintLayout`的方式
其它层 - `LinearLayout`

##### 2. 控件设计

​	通过`cardview`实现圆角，加上灰色bg，实现了类似抖音图标按钮的效果

对比：

![img](/img/in-post/bytedance-app-widgets.png)

##### 3. 右上角菜单设计

​	由于时间有限，并没有实现预想的菜单全部功能，主要实现了更换背景，由于`activityForResult`在新版Android已经不好用，这次采用了`ActivityResultLauncher`的方式

​	具体流程如下：
点击进入权限判断

```java
// 更改背景图
   if (ActivityCompat.checkSelfPermission(requireContext(), Manifest.permission.READ_EXTERNAL_STORAGE)== PackageManager.PERMISSION_GRANTED){
// Toast.makeText(getContext(),"permission-allowed",Toast.LENGTH_LONG).show();
    mGetContent.launch("image/*");
    }else{
    requestStoragePermission();
    }
    return true;
```

如果获取权限则开始选择图片
```java
    ActivityResultLauncher<String> mGetContent = registerForActivityResult(new ActivityResultContracts.GetContent(),
    this::updateUser);
```

获取权限失败，申请获取权限
1. 申请失败，返回
2. 申请成功，开始选择图片





### 2. 启动页面动画和进一步美化



##### 1. 启动页面动画

<html>

<img src="/img/in-post/bytedance-app-lotties.jpeg" width="280" height="400"/>

</html>



比较简单，使用了启动页比较常用的`lottie`，简洁的同时非常美观。

```java
public class LottieActivity extends AppCompatActivity {

    LottieAnimationView lottieAnimationView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_lottie);
        this.getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        lottieAnimationView = findViewById(R.id.lottie);
        new Handler().postDelayed(() -> {
            Intent intent = new Intent(getApplicationContext(), MainActivity.class);
            startActivity(intent);
        },1800);
    }

    @Override
    protected void onPause() {
        super.onPause();
        this.finish();
    }
}
```



##### 2. 进一步美化


为了让界面看起来更好看，我加入了图片穿透到状态栏的显示，使进入软件时和向下滑动后都有更好看的显示效果，特别是向下滑动后不会存在单色状态栏和背景图接触的突兀感。
```xml
<item name="windowActionBar">false</item>
<item name="windowNoTitle">true</item>
<item name="android:windowTranslucentStatus">true</item>
<item name="android:windowDrawsSystemBarBackgrounds">true</item>
<item name="android:statusBarColor">@android:color/transparent</item>
```

对比图：

![img](/img/in-post/bytedance-app-topbar.png)



添加个人介绍展开，三角按钮旋转，比较简单，就不叙述了。

```java
@Override
public void onClick(View view) {
    TextView tv = binding.tvInfo;
    singleLine = !singleLine;
    tv.setSingleLine(singleLine);
    if (singleLine){
        Glide.with(getContext()).load(R.drawable.arrow).into(binding.btnExpand);
    }else{
        Glide.with(getContext()).load(R.drawable.arrow).transform(new Rotate(180)).into(binding.btnExpand);
    }
}
```

其他: 简单动画等等



## 收获



1. 实践完成了一个包含主流技术栈的项目，非常有成就感

2. 与小组一起完成了团队协作，通过git/github 进行同步，有不同于个人写项目的体验

3. 讲课老师认真有趣，收获了知识



![certification](/img/in-post/bytedance-certification.jpg)
