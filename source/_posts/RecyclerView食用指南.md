---
title: RecyclerView食用指南
date: 2020-02-19 20:15:40
tags:
- RecyclerView
categories:
- Android
---
## 写在前面
&emsp;说起来也是好久没有写过博客了，这东西来来回回就忘了，当时的脑热造就了空白的内容，想想多少还是有些不合适。
&emsp;毕竟也是自己的经历，不如写出来分享一下，或者说，备忘吧。

## RecyclerView总述
&emsp;安卓开发过程中总会遇到各种麻烦的事情。在编写浏览器时想要构造一个列表，这里面可以显示网站的标题和链接。这东西看起来感觉非常简单，尤其是在h5界面中编写。但是现在是安卓开发啊弟弟，用法根本不是想象中的那样搞一个模块然后findviewbyid啊。这里我踩了很多坑，并且构造过程真的非常麻烦（自我感觉），所以在这里开一个页面记录下我在这里是怎么解决问题的。
> RecyclerView 是一个增强版的ListView，不仅可以实现和ListView同样的效果，还优化了ListView中存在的各种不足之处（来自CSDN）。
> RecyclerView 能够实现横向滚动，这是ListView所不能实现的（虽然我没用到这个233）

<!--more-->

## 前置条件
&emsp;dependencies中要添加库的引用（以下库可能已过时）：
```java
dependencies {
    compile&nbsp;'com.android.support:appcompat-v7:27.0.2'
    compile&nbsp;'com.android.support:recyclerview-v7:27.0.2'
}
```
&emsp;这里注意下后面的版本号要跟compileSdkVersion一致，不然可能会报错。

## 布局引用
&emsp;在要添加RecyclerView的布局中添加:
```xml
    <linearlayout 
        xmlns:android="http://schemas.android.com/apk/res/android" 
        android:layout_width="match_parent" 
        android:layout_height="match_parent">

        <android.support.v7.widget.recyclerview 
            android:id="@+id/recycler_view" 
            android:layout_width="match_parent" 
            android:layout_height="match_parent"/>
    
    </linearlayout>
```
&emsp;布局文件就到这里了，其实布局是非常简单的。。
&emsp;重点在下面的部分。

## 适配器（Adapter）的创建
### 内容提供
&emsp;首先我们要明确，创建这个适配器是要适配什么东西，在这里，作为一个浏览器，我打算让它提供页面的标题和链接，并且可以让它显示在RecyclerView中。所以我们先要创建最基本的类型，在这里我取名叫WindowInfo，很简单的一个类，并且在这个类中只需要提供setter和getter就行了。
```java
public class WindowInfo {
    private String windowTitle;
    private String windowUrl;

    public WindowInfo(){
        windowTitle = "";
        windowUrl = "";
    };

    public WindowInfo(String title, String url){
        windowTitle = title;
        windowUrl = url;
    }

    public String getWindowTitle(){
        return windowTitle;
    }

    public String getWindowUrl(){
        return windowUrl;
    }

    public void setWindowTitle(String title){
        windowTitle = title;
    }
}
```
&emsp;这里真的没什么说的，自定义的类都可以。
### 适配器创建
&emsp;再新建一个类，我这里取名为WindowInfoAdapter，让这个类继承于RecyclerView.Adapter<WindowInfoAdapter.ViewHolder>，现在爆红先不要紧张，后面会填坑（
&emsp;在这个适配器的类中，我们需要创建一个内部的List表，用来存放外部传进来的提供的内容。既然要把外部的内容传进来，那么必不可少的就是构造函数了：
```java
public WindowInfoAdapter(List<windowinfo> _windowList){
    windowList = _windowList;
}
```
&emsp;但是我们不可能仅仅把参数传进来就完事吧，RecyclerView里面每个项目的排版不要面子的吗？
&emsp;新建一个布局，自己起名叫WindowInfoItem.xml，这个排版是用来设置RecyclerView 中每个项目里面元素的样式的，是不可缺少的。
```xml
<linearlayout 
    xmlns:android="http://schemas.android.com/apk/res/android" 
    ndroid:orientation="vertical" 
    android:layout_width="match_parent" 
    android:layout_height="64dp" 
    android:layout_gravity="center_vertical" 
    android:layout_centervertical="true" >
    <textview 
        android:layout_width="match_parent" 
        android:layout_height="0dp" 
        android:id="@+id/title" 
        android:layout_weight="1" 
        android:layout_marginstart="5dp" 
        android:layout_marginend="5dp" 
        android:gravity="center_vertical" 
        android:textcolor="#000000" 
        android:textsize="20sp" 
        android:singleline="true"/>
    <textview 
        android:layout_width="match_parent" 
        android:layout_height="0dp" 
        android:id="@+id/url" 
        android:layout_weight="1" 
        android:layout_marginstart="5dp" 
        android:layout_marginend="5dp" 
        android:gravity="center_vertical" 
        android:textcolor="#c0c0c0" 
        android:textsize="16sp" 
        android:singleline="true" />
</linearlayout>
```
&emsp;接下来的工作可以说是非常重要了，现在仅仅是将排版排好了，但是其中的部件都不能单独拿出来设置以及初始化呀。所以现在新建一个静态内部类ViewHolder使其继承自RecyclerView.ViewHolder，由它的名字就可以知道，这个类主要是用来承载所有的部件的，所以就可以在这里取得元素的id，并且对其进行初始化了。
```java
static class ViewHolder extends RecyclerView.ViewHolder{
    TextView Title;
    TextView Url;

    public ViewHolder(View view){
        super(view);
        bookmarkTitle = (TextView) view.findViewById(R.id.title);
        bookmarkUrl = (TextView) view.findViewById(R.id.url);
    }
}
```
&emsp;但这还没完呢，机器毕竟不是人，凭什么要按你的样式来？所以我们需要强制指定布局，重写onCreateViewHolder:
```java
@Override
public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.WindowInfoItem, parent, false);
    final ViewHolder viewHolder = new ViewHolder(view);
    return viewHolder;
}
```
&emsp;并且，就算是创建了ViewHolder，但是你并不能改变其内容，因为目前的ViewHolder很广泛，就如同大家的公用rbq一样，那可不行，得指定谁用谁的女朋友啊。所以，onBindViewHolder是必须得重写的。
```java
@Override
public void onBindViewHolder(final ViewHolder holder, final int position) {
    WindowInfo windowInfo = windowList.get(position)
    holder.Url.setText("");
    holder.Title.setText("");
}
```
&emsp;现在看看整个class，会发现在声明那里仍然是爆红的，不要慌，只需要将getItemCount重写就行了：
```java
@Override
public int getItemCount() {
    return windowList.size();
}
```
&emsp;到这里为止，整个适配器就创建完成了，贴上整体的代码：
<details>
    <summary><mark style="background: gray;color: white">点击查看详细代码</mark></summary>
    <pre language="java"><code>
public class WindowInfoAdapter extends RecyclerView.Adapter<windowinfo.viewholder> {
    private List<windowinfo> windowList;

    static class ViewHolder extends RecyclerView.ViewHolder {
        TextView Title;
        TextView Url;

        public ViewHolder(View view){
            super(view);
            siteTitle = (TextView) view.findViewById(R.id.Title);
            siteUrl = (TextView) view.findViewById(R.id.Url);
        }
    }

    public WindowInfoAdapter(List<windowinfo> _windowList){
        windowList = _windowList;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.WindowInfoItem, parent, false);
        final ViewHolder viewHolder = new ViewHolder(view);
        return viewHolder;
    }

    @Override
    public void onBindViewHolder(final ViewHolder holder, int position) {
        WindowInfo windowInfo = windowList.get(position);
        holder.Title.setText("");
        holder.Url.setText("");
    }

    @Override
    public int getItemCount() {
        return windowList.size();
    }
}
    </code></pre>
</details>

## RecyclerView启动！
&emsp;前面bb了一大堆，就是为这里打基础的，其实这里就很简单了，我们回到MainActivity中来，首先要做的是创建三个变量，分别是:
- 内容提供类的一个列表：
```java
private static List<windowinfo> windowInfoList;
```
- 适配器：
```java
private static WindowInfoAdapter windowInfoAdapter; 
```
- RecyclerView:
```java
private RecyclerView windowInfoRecyclerView;
```
&emsp;再将RecyclerView实例化之后，我们只需要执行这几步:
* 先将内容绑定到适配器中：
```java
windowInfoAdapter = new WindowInfoAdapter(windowInfoList);
```
* 再将适配器与RecyclerView绑定：
```java
windowInfoRecyclerView.setAdapter(windowInfoAdapter);
```
* 最后设置一些关于RecyclerView的比较重要的设定（分割线动画什么的）：
```java
windowInfoRecyclerView.setItemAnimator(new DefaultItemAnimator());
windowInfoRecyclerView.addItemDecoration(new DividerItemDecoration(this,DividerItemDecoration.VERTICAL));
```
&emsp;整个过程就结束了，这么一整理下来感觉真简单啊= =||

## 后话
&emsp;RecyclerView其实真的挺重要的，因为在大多数场合都会用到，并且与其他的View也能达到良好的兼容性，可以说很棒了。