---
title: inflate导致的空指针
date: 2019-06-28 19:29:27
tags:  问题总结
---
在一个项目开发中遇到一个手残的问题，但是排查的时候觉得很坑爹，就记录下加深记忆。  
# 背景  
某次开发中有个折叠框的小需求，点击的时候展开，通过动态添加item来实现，每个item上有个分割线，很简单。开发完后编译没问题，但是一运行就崩。  
# 代码  
## Java代码  

```  
contentListLayout.removeAllViews();
        LayoutInflater layoutInflater = LayoutInflater.from(getContext());
        for (CharSequence content : contentList) {
            View viewContent = layoutInflater.inflate(R.layout.layout_collapse_content_item, null);
            tvContent = viewContent.findViewById(R.id.tvContent);
            tvContent.setText(content);
            tvContent.setTextColor(Color.parseColor(contentColor));
            tvContent.setTextSize(contentSize);
            contentListLayout.addView(viewContent);
        }
```  
## item xml代码  

```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">    
    <view
        android:layout_width="match_parent"
        android:layout_height="0.5dp"
        android:background="@color/divideLineColor"/>
    <TextView
        android:id="@+id/tvContent"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="我是内容文字"
        android:paddingBottom="13dp"
        android:paddingTop="13dp"
        android:textSize="14sp" />
</LinearLayout>
```  
# 报错信息  
编译的时候不会报错，运行时报错信息如下：  
android.view.InflateException: Binary XML file  ...
# 问题总结  
从报错信息中可见是在我们inflate的时候空指针了，资源文件名都没问题。最终查看xml文件里面的时候发现分割线的view写成小写了，导致崩溃。  
这里的问题就在于view的v写成小写AS并不会报错，只有在运行时才崩溃，所以排查起来很坑爹。  
总结：不要手残。