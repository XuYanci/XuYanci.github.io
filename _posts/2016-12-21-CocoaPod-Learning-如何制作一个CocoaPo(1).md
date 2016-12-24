---
layout: post
title: "CocoaPod-Learning-如何制作一个CocoaPod(1)"
date: 2016-12-22
excerpt: "CocoaPod-Learning-如何制作一个CocoaPod(1)"
tags: [github cocoapod]
comments: true
---

 
### 创建一个仓库来存放库文件:
<figure class="">
    <img src="/assets/img/2016-12-21-how_to_make_an_cocoapod/1.png">
</figure>


### 建立目录结构:

	$ 	git clone https://github.com/XuYanci/YCPodTestLibrary 
	
	$	cd YCPodTestLibrary

	$	mkdir PodTestLibrary

	$	cd PodTestLibrary

	$	mkdir Assets	(ps:存放资源文件、例如图片)

	$	mkdir Classes  (ps:存放库源文件,例如Helper.h,Helper.m)

	$	cd ../  		(ps:返回YCPodTestLibrary 根目录)

	$	touch LICENSE 

	$	touch README	(ps:自定义文字内容)

	$	touch YanciXuPodTestLibrary.podspec

<figure class="">
	<figcaption>目录树结构</figcaption>
    <img src="/assets/img/2016-12-21-how_to_make_an_cocoapod/3.png">
</figure>


##### LICENSE文件:
 
	Copyright (c) 2016 who <who@gmail.com>
	Permission is hereby granted, free of charge, to any person obtaining a copy
	of this software and associated documentation files (the "Software"), to deal
	in the Software without restriction, including without limitation the rights
	to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
	copies of the Software, and to permit persons to whom the Software is
	furnished to do so, subject to the following conditions:
	The above copyright notice and this permission notice shall be included in
	all copies or substantial portions of the Software.
	THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
	IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
	FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
	AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
	LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
	OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
	THE SOFTWARE.


##### PodSepc文件  
	#
	# Be sure to run `pod lib lint podTestLibrary.podspec' to ensure this is a
	# valid spec before submitting.
	#
	# Any lines starting with a # are optional, but their use is encouraged
	# To learn more about a Podspec see http://guides.cocoapods.org/syntax/podspec.html
	#
	Pod::Spec.new do |s|
	  s.name             = 'YanciXuPodTestLibrary' 
	  s.version          = '0.1.0'
	  s.summary          = 'A short description of YCPodTestLibrary.'
	# This description is used to generate tags and improve search results.
	#   * Think: What does it do? Why did you write it? What is the focus?
	#   * Try to keep it short, snappy and to the point.
	#   * Write the description between the DESC delimiters below.
	#   * Finally, don't worry about the indent, CocoaPods strips it!
	  s.description      = <<-DESC
	TODO: Add long description of the pod here.
	                       DESC
	  s.homepage         = 'https://github.com/XuYanci/YCPodTestLibrary.git' ()
	  # s.screenshots     = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'
	  s.license          = { :type => 'MIT', :file => 'LICENSE' }
	  s.author           = { 'XuYanci' => 'grandy.wind@gmail.com' }
	  # @note 第一步生成gitrepo地址
	  s.source           = { :git => 'https://github.com/XuYanci/YCPodTestLibrary.git', :tag => s.version.to_s }
	  # s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'
	  s.ios.deployment_target = '8.0'
	  # @note 第一步生成的目录,用于存放Lib源文件 (.h,.m)
	  s.source_files = 'podTestLibrary/Classes/**/*'
	  # @note 第一步生成的目录,用于存放Lib资源文件 (.png)
	  s.resource_bundles = {
	     'podTestLibrary' => ['podTestLibrary/Assets/*.png']
	   }
	  # s.public_header_files = 'Pod/Classes/**/*.h'
	  # s.frameworks = 'UIKit', 'MapKit'
	  # s.dependency 'AFNetworking', '~> 2.3'
	end

###  上传Lib到Github Repo 
	$  cd	YCPodTestLibrary	
	$  git add . 
	$  git commit -m "release 0.1.0"
	$  git push origin master   (ps:https://github.com/XuYanci/YCPodTestLibrary.git)
	$  git tag -a 0.1.0 
	$  git push -tags
	
### 验证podsepc是否有效
 
	$	pod lib lint --allow-warnings  
	
 <figure class="">
 	<figcaption>验证通过</figcaption>
    <img src="/assets/img/2016-12-21-how_to_make_an_cocoapod/4.png">
</figure>




### 推送到cocoapod 
 
	$ 	pod trunk push [name].podspec 
 
<figure class="">
	<figcaption>🚀 祝贺你,你可以搜索和使用你的pod了</figcaption>
    <img src="/assets/img/2016-12-21-how_to_make_an_cocoapod/2.png">
</figure>

### Reference ###
* [https://guides.cocoapods.org](https://guides.cocoapods.org)

* [https://my.oschina.net/u/727843/blog/392784](https://my.oschina.net/u/727843/blog/392784)