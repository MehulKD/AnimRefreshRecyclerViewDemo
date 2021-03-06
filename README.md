# AnimRefreshRecyclerViewDemo
声明：本项目上传以来，经常有朋友反馈说有时候快速下拉会崩溃，而本人技术有限，暂时无法修复此Bug，因此请大家不要在自己的项目中直接使用本项目，如果有哪位大神可以修复此Bug，还请告知。谢谢各位对本人的支持！

下拉刷新和上拉加载更多的RecyclerView，具有下拉和刷新动画。
效果：

嗯...看起来有点卡，截图软件的问题：

![带动画的下拉刷新RecyclerView](http://img.blog.csdn.net/20150808145428151)

上图中演示了三种不同的布局和下拉效果，三种布局和三种下拉效果可以通过Header的设置任意组合。

图中普通列表是ListView样式，没有设置Header和Footer，使用默认的下拉刷新和上拉加载。

宫格列表使用的是自定义Header和Footer的下拉刷新和上拉上拉加载，并设置了下拉使放大的图片。

瀑布流列表使用的是自定义Header和Footer的下拉刷新和上拉上拉加载，没有设置了下拉使放大的图片，使用默认的刷新动画。

**用法：**

Gradle:

```xml
dependencies {
    compile 'com.sch.rfview:AnimRefreshRecyclerView:1.0.1'
}
```

Eclipse的同学们可以自己下载源码拷贝java文件到自己的工程（别忘了引用RecyclerView的包哦）。

代码中的配置参考下面的用法代码片段，除了RecyclerView自带的方法，其他方法都是可选的。

项目地址：https://github.com/shichaohui/AnimRefreshRecyclerViewDemo

项目中包含一个demo（普通Android工程）和Android Library，感兴趣的同学可以自己下载源码和Demo。

**用法代码片段：**

* 根据列表的不同效果选择不同的布局管理器：
```java
// 使用重写后的线性布局管理器
mRecyclerView.setLayoutManager(new AnimRFLinearLayoutManager(this));

// 使用重写后的格子布局管理器
mRecyclerView.setLayoutManager(new AnimRFGridLayoutManager(this, 2));

// 使用重写后的瀑布流布局管理器
mRecyclerView.setLayoutManager(new AnimRFStaggeredGridLayoutManager(3, StaggeredGridLayoutManager.VERTICAL));
```
* 设置Header和Footer：
```java
// 头部
headerView = LayoutInflater.from(this).inflate(R.layout.header_view, null);
// 脚部
footerView = LayoutInflater.from(this).inflate(R.layout.footer_view, null);

// 添加头部和脚部，如果不添加就使用默认的头部和脚部（头部可以有多个）
mRecyclerView.addHeaderView(headerView);
// 设置头部的最大拉伸倍率，默认1.5f，必须写在setHeaderImage()之前
mRecyclerView.setScaleRatio(2.0f);
// 设置下拉时拉伸的图片，不设置就使用默认的
mRecyclerView.setHeaderImage((ImageView) headerView.findViewById(R.id.iv_hander));
mRecyclerView.addFootView(footerView);
```
可以通过`addHeaderView()`和`setHeaderImage()`方法任意组合下拉效果，可以调用多次`addHeaderView()`方法添加多个头部，但是`setHeaderImage()`方法最多被调用一次。

最多调用一次`addFootView()`方法，即最多设置一个FooterView。

* 其他设置：
```java
// 设置刷新动画的颜色（可选）
mRecyclerView.setColor(Color.RED, Color.WHITE);
// 设置头部恢复动画的执行时间，默认1000毫秒（可选）
mRecyclerView.setHeaderImageDurationMillis(1200);
// 设置拉伸到最高时头部的透明度，默认0.5f（可选）
mRecyclerView.setHeaderImageMinAlpha(0.6f);

// 设置适配器
mRecyclerView.setAdapter(new MyAdapter());

// 设置刷新和加载更多数据的监听，分别在onRefresh()和onLoadMore()方法中执行刷新和加载更多操作
mRecyclerView.setLoadDataListener(new AnimRFRecyclerView.LoadDataListener() {
    @Override
    public void onRefresh() {
	    // 开启线程刷新数据
        new Thread(new MyRunnable()).start();
    }

    @Override
    public void onLoadMore() {
	    // 开启线加载更多数据
        new Thread(new MyRunnable()).start();
    }
});
```

* 在刷新和加载过更多完成之后调用代码停止动画：
```java
// 刷新完成后调用，必须在UI线程中
mRecyclerView.refreshComplate();

// 加载更多完成后调用，必须在UI线程中
mRecyclerView.loadMoreComplate();
```

Tips：

1.若在使用过程中发现`adapter.notifyDataSetChange()`等更新数据的方法无效，可使用`recyclerView.getAdapter()`获取当前使用的Adapter，并使用获取到到Adapter更新数据。

2.解决Android 5.0兼容问题：
Gradle依赖包的版本没有更新，要适配的话请自行修改代码，将`AnimView.java`中的`setVisibility(int visibility)`这个方法改成如下：
```
@Override
public void setVisibility(int visibility) {
    isShow = visibility == VISIBLE;
    if (isShow) {
        super.setVisibility(VISIBLE);
    }
    postInvalidate();
}
```
