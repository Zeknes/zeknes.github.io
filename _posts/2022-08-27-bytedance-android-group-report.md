---
layout: post
title: "字节跳动青训营byte组结业项目答辩汇报文档"
subtitle: "Bytedance android training byte team final project report"
author: "Lin"
header-style: text
tags:
  - Android(安卓)
  - UI
  - Room数据库
  - Retrofit
  - Tiktok(抖音)
---



组长对项目贡献的最多, 我跟着也学到了很多. 这里再次表示感谢.



## 项目实现



### 1. 技术选型



**1.1. 网络请求**

采用`Retrofit` + `RxJava` + `Gson` 的技术栈.

**1.2. 数据库储存**

采用Google的`Room`进行数据的持久化储存.

**1.3. 图片加载**

使用`Glide`图片加载框架对图片进行加载, 更有效的保证了列表滑动时的流畅, 以及自动对图片进行压缩, 缓存.

**1.4. 开发语言**

使用`Java`语言进行开发.



### 2. 架构设计



**2.1. 项目框架**

`mvvm`

**2.2. 设计模式**

工厂模式, 适配器模式, 观察者模式等.



### 3. 开发文档



##### 功能需求

**1. 榜单模块**

- 电影, 电视剧, 综艺实时榜单.
- 电影, 电视剧, 综艺历史榜单查询.

**2. 个人中心模块**

- 个人页面展示.
- 个人页面粉丝与关注列表.
- 个人页面已发布视频列表.
- 个人页面已发布视频列表个人页面已发布视频列表详情页.



##### 技术要求

**1. 网络请求**

- 网络请求采用`RxJava` + `Retrofit`.

**2. 数据库存储**

- 使用谷歌推荐的`Room数据库`框架 (`Rxjava` + `Room`).

**3. 项目框架**

- 采用`MVVM`框架.

**4. 团队协同**

- 使用`Github`进行协同开发.

**5. 其他要求**

- 选择性进行列表分页.
- 网络请求后保存数据到数据库.
- 无网络时请求展示数据库的数据.
- 如果无网络且数据库无缓存, 则显示相应提示. ( 如网络连接失败 ).



### 4. 项目代码介绍



##### 1. 架构

采用`MVVM`的框架, 借鉴了谷歌推荐的应用架构指南.

分为两层: 界面层和数据层, `ui`布局和数据之间通过`ViewModel`建立关系, 同时`ViewModel`是属于界面层, 引用谷歌的一张图片如下. 对于数据层, 主要为数据仓库, 数据仓库负责统一调度网络请求仓库和数据库仓库, 以便在网络请求失败的情况下可以使用数据库的缓存.



<html>

<img src="/img/in-post/google-project-structure.png" width=300 height="300"/>

</html>



代码目录结构如下图所示

- **douyinapi**

  与抖音`SDK`有关的类, 如请求授权和授权回调Activity.

- **logic**

  - `database`

    数据库文件夹, 主要为实体类, DAO接口, 数据库类, 数据转换器.

  - `dataSource`

    数据对外暴露的接口, 定义了需对外暴露的方法.

  - `factory`

    存放工厂类.

  - network

    与网络请求有关的类, 如作为回调的实体类对象, 网络请求接口, 自定义错误处理.

  - `repository`

    存放数据仓库, 用于调度网络请求与数据库储存, 实现dataSource接口.

- **ui**

  存放与界面层有关的类, 如`ViewModel,` `Activity`, `Fragment`, `Adapter`等.

- **util**

  工具类.

- **widget**

  自定义`view`等.



![](/img/in-post/bytedance-code-structure.png)



##### 2. 网络请求

网络请求方面, 通过`RxJava`的特性对`Retrofit`进行了一些封装, 对请求结果进行错误验证.

```java
public abstract class DouYinBaseData {

    // 错误码
    @SerializedName("error_code")
    @Ignore
    protected Long errorCode;

    // 错误码描述
    @Ignore
    protected String description;

    public Long getErrorCode() {
        return errorCode;
    }

    public String getDescription() {
        return description;
    }
}

```

```java
public class DouYinResponse<T extends DouYinBaseData> {

    // 回调核心数据
    private T data;

    private String message;

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    @NonNull
    @Override
    public String toString() {
        return "DouYinResponse{" +
                "data=" + data +
                ", message='" + message + '\'' +
                '}';
    }
}

```

```java
@NonNull
    @Override
    public ObservableSource<T> apply(Observable<DouYinResponse<T>> upstream) {
        return upstream
                .doOnSubscribe(disposable -> {
                    if (compositeDisposable != null) {
                        compositeDisposable.add(disposable);
                    }
                })
                .onErrorResumeNext(throwable -> {
                    return Observable.error(NetException.handleException(throwable));
                })
                .flatMap((Function<DouYinResponse<T>, ObservableSource<T>>) tDouYinResponse -> {
                    System.out.println("tDouYinResponse:" + tDouYinResponse);
                    T data = tDouYinResponse.getData();
                    // 请求成功
                    if (data != null && data.getErrorCode() == 0) {
                        return Observable.just(data);
                    }
                    if (data == null) {
                        return Observable.error(new NetException("请求失败"));
                    }
                    System.out.println("error:" + data.getDescription());
                    return Observable.error(new NetException(data.getDescription()));
                })
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread());
    }

```



##### 3. 数据请求

采用`MVVM`的框架, 遵循谷歌推荐的方式, 划分`界面层`和`数据层`, 两者之间通过`ViewModel`连接, 数据层由数据仓库( `Repository` ) 来负责, 数据仓库根据请求成功与否来决定使用网络请求的数据还是数据库中缓存的数据, 即由数据仓库来调度数据库与网络请求模块.



以获取榜单为例

```java
public class RankItemRepository implements RankItemDataSource {

    ClientTokenDataSource clientTokenDataSource;

    RankItemDao rankItemDao;

    public RankItemRepository(ClientTokenDataSource clientTokenDataSource, RankItemDao rankItemDao) {
        this.clientTokenDataSource = clientTokenDataSource;
        this.rankItemDao = rankItemDao;
    }

    @Override
    public Maybe<List<RankItem>> queryMovie(int type, int version) {
        // 首先请求获取clientToken，如果数据库的未过期会从数据库中拿，数据库过期会联网申请
        return clientTokenDataSource.getClientToken()
                .flatMap((Function<ClientToken, MaybeSource<List<RankItem>>>) clientToken -> {
                    // 成功获取ClientToken
                    Retrofit retrofit = NetWorkFactory.provideRetrofit();
                    RankService rankService = retrofit.create(RankService.class);
                    // 根据是否需要版本号选择请求方式
                    Observable<DouYinResponse<RankData<RankItem>>> observable
                            = version == 0 ? rankService.getRank(clientToken.getAccessToken(), type) // 获取最新
                            : rankService.getRank(clientToken.getAccessToken(), type, version); // 根据版本号获取
                    return observable
                            .compose(ResponseTransformer.obtain())
                            .map(rankItemRankData -> {
                                // 如果map被调用，则说明联网请求成功，将结果异步缓存到数据库
                                // 保存数据到数据库，并清空之前的缓存
                                Disposable disposable = rankItemDao.delete(type, version)
                                        .subscribeOn(Schedulers.io())
                                        .subscribe(new Action() {
                                            @Override
                                            public void run() {
                                                // 给榜单设置版本号
                                                for (RankItem rankItem : rankItemRankData.getList()) {
                                                    rankItem.setVersion(version);
                                                }
                                                rankItemDao.insert(rankItemRankData.getList())
                                                        .subscribeOn(Schedulers.io())
                                                        .subscribe();
                                            }
                                        });
                                return rankItemRankData.getList();
                            })
                            .singleElement();
                })
                .onErrorResumeNext((Function<Throwable, MaybeSource<List<RankItem>>>) throwable -> {
                    // 如果请求出现异常(联网失败等情况)
                    return rankItemDao.queryRank(type, version)
                            .subscribeOn(Schedulers.io())
                            .observeOn(AndroidSchedulers.mainThread())
                            .defaultIfEmpty(new ArrayList<>())
                            // 当数据库无数据时会发送这条默认数据
                            .flatMap((Function<List<RankItem>, MaybeSource<List<RankItem>>>) rankItems -> {
                                // 说明本地数据库无数据
                                if (rankItems.size() == 0) {
                                    // 抛出上面的错误
                                    return Maybe.error(throwable);
                                }
                                // 说明是数据库中的数据
                                return Maybe.just(rankItems);
                            });
                });
    }

}

```



##### 4. 加载

因为基本每个列表都需要用到 "加载更多" 的功能, 因此封装了一个特殊的`adaptor`, 可以添加顶部`Header`以及底部`Footer`, 默认内置了底部`Footer,` 并外放方法可以设置`Footer`处于"`正在加载中`" 还是"`没有更多了`" 的状态.

```java
public class ExtendAdapter {

    // 头部适配器
    private final RecyclerView.Adapter<? extends RecyclerView.ViewHolder> headerAdapter;

    // 核心适配器
    private final RecyclerView.Adapter<? extends RecyclerView.ViewHolder> rootAdapter;

    // 尾部适配器
    private final FooterAdapter footerAdapter;

    // 结果适配器
    private final ConcatAdapter adapter;

    public ExtendAdapter(RecyclerView.Adapter<? extends RecyclerView.ViewHolder> rootAdapter) {
        this(null, rootAdapter, false);
    }

    public ExtendAdapter(RecyclerView.Adapter<? extends RecyclerView.ViewHolder> rootAdapter, boolean needFooter) {
        this(null, rootAdapter, needFooter);
    }

    public ExtendAdapter(RecyclerView.Adapter<? extends RecyclerView.ViewHolder> headerAdapter, RecyclerView.Adapter<? extends RecyclerView.ViewHolder> rootAdapter, boolean needFooter) {
        this.headerAdapter = headerAdapter;
        this.rootAdapter = rootAdapter;
        this.footerAdapter = needFooter ? getFooterAdapterInstance() : null;
        // 构建一个ConcatAdapter来串联三个适配器
        adapter = new ConcatAdapter(rootAdapter);
        if (headerAdapter != null) {
            adapter.addAdapter(0, headerAdapter);
        }
        if (footerAdapter != null) {
            adapter.addAdapter(footerAdapter);
        }
    }

    /*
        获取一个FooterAdapter适配器实例
     */
    private static FooterAdapter getFooterAdapterInstance() {
        return new FooterAdapter();
    }


    /*
        更改Footer状态
     */
    public void changeFooter(int status) {
        if (footerAdapter != null) {
            footerAdapter.changeStatus(status);
        }
    }

    /*
        获取Adapter实例
     */
    public ConcatAdapter getAdapter() {
        return adapter;
    }
}

```

```java

public class FooterAdapter extends RecyclerView.Adapter<FooterAdapter.FooterHolder> {

    // 状态：0 没有更多了  1 加载更多
    private int status;

    public FooterAdapter() {
    }

    public FooterAdapter(int status) {
        this.status = status;
    }

    public void changeStatus(int status) {
        this.status = status;
        notifyItemChanged(0);
    }

    @NonNull
    @Override
    public FooterHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.list_item_footer, parent, false);
        return new FooterHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull FooterHolder holder, int position) {
        holder.changeStatus(status);
    }

    @Override
    public int getItemCount() {
        return 1;
    }

    public static class FooterHolder extends RecyclerView.ViewHolder {

        ListItemFooterBinding binding;

        public FooterHolder(@NonNull View itemView) {
            super(itemView);
            binding = ListItemFooterBinding.bind(itemView);
        }

        public void changeStatus(int status) {
            if (status == 1) {
                binding.loading.setVisibility(View.VISIBLE);
                binding.messageText.setText("正在加载中...");
            } else {
                binding.loading.setVisibility(View.GONE);
                binding.messageText.setText("没有更多了");
            }
        }

    }

}

```



##### 5. 视频播放

对于个人视频播放详情页, 采用`webview`控件, 并动态注入`JavaScript`代码来隐藏一些不重要的元素以及实现进入自动播放的功能.

```java
public class MyWebViewClient extends WebViewClient {
    private final MyWebView myWebView;

    public MyWebViewClient(MyWebView myWebView) {
        this.myWebView = myWebView;
    }

    public boolean shouldOverrideUrlLoading(WebView webView, String url) {
        if (url.startsWith("http://") || url.startsWith("https://")) {
            webView.loadUrl(url);
        }
        return false;
    }

    public void onPageFinished(WebView view, String url) {
        myWebView.measure(0, 0);
        new Handler().postDelayed(() -> view.loadUrl(getHideNoImportantElement()), 500);
    }

    // 隐藏抖音播放时无用的元素
    private String getHideNoImportantElement() {
        String jsPrefix = "javascript:(function() {";
        String js = "document.getElementsByClassName('footer')[0].style.display = 'none';"; // 隐藏底部信息
        js += "document.getElementsByClassName('login-header')[0].style.display = 'none';"; // 隐藏顶部logo区
        js += "document.getElementsByClassName('right')[0].style.display = 'none';"; // 隐藏右边视频数据区
        js += "document.getElementsByClassName('end-model')[0].style.display = 'none';"; // 隐藏播放完提示跳转抖音的区域
        js += "document.getElementsByClassName('video-player')[0].click();"; // 自动播放
        String jsSuffix = "})()";//end-model
        return jsPrefix + js + jsSuffix;
    }

```





## 测试



### 1. 功能测试



**榜单功能测试**

功能要求: 查询当前版本 (按版本号或者查询最新) 的榜单数据, 并显示在表单上. 如果网络请求成功, 则显示网络请求的数据, 并将结果缓存到数据库中. 并显示到列表, 如果网络请求失败, 先查询数据库中是否有缓存数据, 如果数据库中有缓存的数据, 将数据库中的缓存显示到列表. 如果数据库中没有数据, 则在界面上提示 "网络请求失败, 请刷新".



**因果图法:**



条件列表:

1. 网络请求成功
2. 网络请求失败
3. 数据库有缓存
4. 数据库没有缓存



结果列表:

1. 数据显示到列表
2. 提示 "网络请求失败, 请刷新"



| 输入条件 | 预期结果 | 测试结果 |
| :------: | :------: | :------: |
|   1, 3   |    a     |    a     |
|   1, 4   |    a     |    a     |
|   2, 3   |    a     |    a     |
|   2, 4   |    b     |    b     |



### 2. 性能测试



**GPU渲染模式分析图形**

![gpu-test](/img/in-post/bytedance-gpu-test.png)



根据图片分析, 滑动过程中基本不会导致卡顿的情况, 但还有优化点.

1. 可以在滑动停止的时候才进行图片加载, 这样可以进一步提高流畅性. 





## 演示



### 1. 截图展示

![](/img/in-post/bytedance-android-app-screenshot.png)

![](/img/in-post/bytedance-android-app-screenshot2.png)



<html>

<video width="300" height="700" controls>
    <source src="/img/in-post/bytedance-app-video-demo.mp4" type="video/mp4">
</video>

</html>



## 项目总结和反思



1. 目前仍存在的问题
   1. 处于私密状态的视频播放不了, 应该隐藏此类视频或者在播放的时候提示"不可播放"
   2. 视频播放使用的是 `webview`, 可以通过一些自定义配置提高 `webview`的性能及体验
2. 已识别的优化项目
   1. 目前几乎每个列表都需要加载更多的功能, 可以提取重复功能, 二次封装`RecyclerView`
3. 架构演进的可能性
   1. 分离架构 -> 服务化架构





![](/img/in-post/bytedance-certification.jpg)
