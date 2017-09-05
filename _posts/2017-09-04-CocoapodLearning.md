---
layout: post
title: "CocoapodLearning"
date: 2017-09-04
excerpt: "CocoapodLearning"
tags: [cocoapod]
comments: true
---

### Specs ### 

**Podspec** 描述了一个Pod库的版本。 一个Pod， 在大部分时间里，会拥有很多Specs。它包含了从哪里获取源码，使用什么文件，应用什么设置以及其他元数据例如它的名称，版本以及描述等。

*Example*

```ruby
Pod::Spec.new do |spec|
spec.name             = 'Reachability'
spec.version          = '3.1.0'
spec.license          = { :type => 'BSD' }
spec.homepage         = 'https://github.com/tonymillion/Reachability'
spec.authors          = { 'Tony Million' => 'tonymillion@gmail.com' }
spec.summary          = 'ARC and GCD Compatible Reachability Class for iOS and macOS.'
spec.source           = { :git => 'https://github.com/tonymillion/Reachability.git', :tag => 'v3.1.0' }
spec.source_files     = 'Reachability.h,m'
spec.framework        = 'SystemConfiguration'
spec.requires_arc     = true
end
```

### Specs Repo ###

**Specs Repo** 是一个GitHub上的仓库，包含所有可用Pods。每一个库有着单独的文件夹，里面包含那个库中可用版本的子文件夹。

*Example*

![8](/assets/img/2017-09-04-CocoapodLearning/8.png)

### Sub Specs ###

**Subspecs** 是一种分离Podspec功能的方式，允许用户安装你库中的某部分功能。

*Example*

```ruby
Pod::Spec.new do |spec|
spec.name          = 'ShareKit'
spec.source_files  = 'Classes/ShareKit/{Configuration,Core,Customize UI,UI}/**/*.{h,m,c}'
# ...

spec.subspec 'Evernote' do |evernote|
evernote.source_files = 'Classes/ShareKit/Sharers/Services/Evernote/**/*.{h,m}'
end

spec.subspec 'Facebook' do |facebook|
facebook.source_files   = 'Classes/ShareKit/Sharers/Services/Facebook/**/*.{h,m}'
facebook.compiler_flags = '-Wno-incomplete-implementation -Wno-missing-prototypes'
facebook.dependency 'Facebook-iOS-SDK'
end
# ...
end
```

**pod 'ShareKit'** 意味着安装整个库，**pod 'ShareKit/Facebook'** 意味着安装库中的Facebook部分。

####  Sub Module ####

当你在仓库中有某些子模块，你需要设置 **s.souce**中的key **:submodules** 为true。 然后你可以使用上述的方式来使用它。

*Example*

```ruby
Pod::Spec.new do |spec|
spec.name          = 'SDLoginKit'
spec.source        =  { 
:git => 'https://github.com/dulaccc/SDLoginKit.git',
:tag => '1.0.2', 
:submodules => true 
}
# ...

spec.subspec 'SDKit' do |sdkit|
sdkit.source_files = 'SDKit/**/*.{h,m}'
sdkit.resources    = 'SDKit/**/Assets/*.png'
end
# ...
end
```

### Private Pods ###

1. 创建一个私有的Spec repo
2. 添加你的私有仓库到你的CocoaPods安装位置
3. 添加你的Podspec到你的repo
4. 就是这样!

### 团贷网私有库 ###

*Gitlab - 创建一个私有的Spec repo*

![1](/assets/img/2017-09-04-CocoapodLearning/1.png)

*添加你的私有仓库到你的CocoaPods安装位置*

![2](/assets/img/2017-09-04-CocoapodLearning/2.png)

*Gitlab - 创建一个TDWLoanToolCaculator库*

![6](/assets/img/2017-09-04-CocoapodLearning/6.png)

*克隆TDWLoanToolCaculator到本地，新建TDWLoanToolCaculatr.podspec、LICENSE、等文件*

![3](/assets/img/2017-09-04-CocoapodLearning/3.png)

*TDWLoanToolLoanCaculator.podspec内容如下*

```shell
#
# Be sure to run `pod lib lint podTestLibrary.podspec' to ensure this is a
# valid spec before submitting.
#
# Any lines starting with a # are optional, but their use is encouraged
# To learn more about a Podspec see http://guides.cocoapods.org/syntax/podspec.html
#
Pod::Spec.new do |s|
s.name             = 'TDWLoanToolCaculator'
s.version          = '0.0.1'
s.summary          = 'Calculator of tuandaiwang loan tools'
s.description      = <<-DESC
TDWLoanToolCaculator is an loan tool calculator.
DESC
s.homepage         = "http://git.tuandai888.com/XuLiJun/TDWLoanToolCaculator.git"
s.screenshots      = '',
s.license          = 'Code is MIT, then custom calculator licenses.'
s.author           = { 'XuYanci' => 'grandy.wind@gmail.com' }
s.source           = { :git => 'http://git.tuandai888.com/XuLiJun/TDWLoanToolCaculator.git', :tag => s.version.to_s }
s.ios.deployment_target = '8.0'
s.source_files = 'TDWLoanToolCaculator/Classes/**/*'
#s.resource_bundles = {
#   'TDWLoanToolCaculator' => ['TDWLoanToolCaculator/Assets/*.png']
# }
# s.public_header_files = 'Pod/Classes/**/*.h'
# s.frameworks = 'UIKit', 'MapKit'
# s.dependency 'AFNetworking', '~> 2.3'
end
```

*验证一下是否可用*

![5](/assets/img/2017-09-04-CocoapodLearning/5.png)

*提交代码，打上标签0.0.1,然后添加你的Podspec到你的私有库*

```shell
git add .
git commit -m "init repo"
git tag '0.0.1'    
git push --tags
git push origin master
pod repo push TDWSpecs TDWLoanToolCaculator.podspec
```

*就是这样使用*

![7](/assets/img/2017-09-04-CocoapodLearning/7.png)

*小伙伴们试试看*

```Shell
第一步: pod repo add TDWSpecs http://<address>/TDWSpecs.git
第二步: 在Podfile里面添加
source 'https://github.com/CocoaPods/Specs.git'
pod 'TDWLoanToolCaculator'
应该就可以用了!
```

### 参考链接   ###

http://guides.cocoapods.org/making/specs-and-specs-repo.html#how-does-the-specs-repo-work

http://guides.cocoapods.org/making/private-cocoapods.html

http://www.jianshu.com/p/ddc2490bff9f

