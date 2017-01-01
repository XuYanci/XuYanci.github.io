---
layout: post
title: "UIKit-Learning-UIScrollView And AutoLayout"
date: 2017-01-01
excerpt: "UIKit-Learning-UIScrollView And AutoLayout"
tags: [iOS UIScrollView AutoLayout]
comments: true
---


Ques: UIScrollView 是如何使得其内容视图滚动显示的。

Answser: 通过改变UIScrollView自身的Position,也就是bounds。

Ques: Frame 和 Bound 的区别是什么呢? 

Answser: 

* Frame 是用来描述视图的位置以及尺寸 (在父视图的坐标系统)
* Bound 是用来描述视图的位置以及尺寸 (在自己的坐标系统)
	
Ques: UIScrollView 如何实现自动布局呢?

Answser: 

UIScrollView 通过修改它的bounds来滚动它的内容视图,如何使得能在自动布局下工作呢,这是通过在scrollView内的left,right,top,bottom edge,即是contentView的edge。

所以我们需要建立一个UIScrollView内置子视图(contentView)来设置它的edge.

代码如下:


{% highlight objc %}

	UIScrollView *scrollView = [UIScrollView new];
	[self.view addSubview:scrollView];
	[sclView mas_makeConstraints:^(MASConstraintMaker *make) {
		make.edges.equalTo(weakSelf.view);
	}];
		
	UIView *contentView = [UIView new];
	[sclView addSubview:contentView];
	[contentView mas_makeConstraints:^(MASConstraintMaker *make) {
	  	make.edges.equalTo(sclView);
	  	make.centerX.equalTo(sclView);
	}];
	
	/*! Add your subview to the contentView */

{% endhighlight %}


Reference: 

[UIScrollView With AutoLayout](https://developer.apple.com/library/content/technotes/tn2154/_index.html)



