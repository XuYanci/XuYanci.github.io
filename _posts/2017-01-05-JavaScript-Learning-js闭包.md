---
layout: post
title: "JavaScript-Learning-js闭包"
date: 2017-01-12
excerpt: "JavaScript-Learning-js闭包"
tags: [js closure]
comments: true
---

目录

1. 块级作用域与函数级作用域 
2. 闭包基本知识 
3. 作用域基本知识
4. IIFE 立即执行的函数表达式  
5. let 关键字


###  块级作用域与函数级作用域
> 任何一对花括号（｛和｝）中的语句集都属于一个块，在这之中定义的所有变量在代码块外都是不可见的，我们称之为块级作用域。
函数作用域就好理解了，定义在函数中的参数和变量在函数外部是不可见的。


{% highlight objc %}
 for (int i = 0; i < 5; i++) 
 {
   	 /* 这里的i可见  */	  
 }
 NSLog(@"%d",i); /* 这里的i未定义 */
{% endhighlight %}


{% highlight js %}
 for (var i = 0; i < 5; i++) 
 {	
 	/* 这里的i可见*/	
 }
alert(i);	/* 这里的i可见 */    
{% endhighlight %}

得出结论:
 
  js是函数级别作用域。

### 闭包基本知识
 * 闭包的解释
   
   >闭包是指那些能够访问独立(自由)变量的函数 (变量在本地使用，但定义在一个封闭的作用域中)。换句话说，这些函数可以“记忆”它被创建时候的环境。
   

 * 闭包的产生
   
   >由于 JavaScript 中，函数是对象，对象是属性的集合，而属性的值又可以是对象，则在函数内定义函数成为理所当然，如果在函数 func 内部声明函数 inner，然后在函数外部调用 inner，这个过程即产生了一个闭包。
 	
{% highlight js %}

	function outer() {
		var i = 100; // 定义变量i 
		function inner() { // 内部函数 
			alert(i);
		}
		return inner;
	}
		
	var func = outer(); // 产生一个闭包 
	func(); // alert(100)

{% endhighlight %}

### 作用域基本知识
 * 作用域的解释
 
   >在 JavaScript 中，变量的作用域是由它在源代码中所处位置决定的（显然如此），并且嵌套的函数可以访问到其外层作用域中声明的变量。
    作用域链的原理和原型链很类似，如果这个变量在自己的作用域中没有，那么它会寻找父级的，直到最顶层。
 
  {% highlight js %}

	/*! 定义全局变量 g_i */
	var g_i = 101;
	function outer() {
		/*! 定义变量i */
		var i = 100; 
		/*! 内部函数 */
		function inner() {
			alert(i + g_i);
		}
		return inner;
	}
	
	var func = outer(); 
	func(); // alert(201)
	
  {% endhighlight %}


###  IIFE 立即执行的函数表达式
 * IIFE的解释
 
   >IIFE (立即调用函数表达式) 是一个 JavaScript 函数 ，它会在定义时立即执行。
   IIFE的引用主要是限制变量的生命周期,以及可以用来锁定值并且有效的保存当时的状态。

 * IIFE的应用
{% highlight js %}
var g_i = 101;
function outer() {
	/*! 定义变量i */
	var i = 100; 
	/*! 内部函数 */
	function inner() {
	/*! 定义变量j*/
	var j = 101;
	alert(i + g_i);
	alert(j); /*! 这里可以访问j变量*/
	};
	return inner;
}; 	

var func = outer();
func(); // alert (201) , alert(101)
{% endhighlight %}
如何限制变量j的生命周期的,通过引用一个立即调用函数表达式。
{% highlight js %}
var g_i = 101;
function outer() {
	/*! 定义变量i */
	var i = 100; 
	/*! 内部函数 */
	function inner() {
	/*! 这里引用立即调用函数表达式 */
	(function() {
	/*! 定义变量j*/
	var j = 101;
	alert(i + g_i);
	)();
	alert(j); /*! 这里无法访问j变量*/
};
	return inner;
}; 	

var func = outer();
func(); // alert (201)

{% endhighlight %}

下面看一个例子
{% highlight js %}
var indicators = document.getElementsByTagName("li");
for (var i = 0; i < indicators.length; i++) {
	 var trigger = function(){
		 indicators[i].onclick = function() {
		 	alert(i);
		 }
	}
	trigger();
}

{% endhighlight %}

当你点击指示器的时候,我们会发现执行结果是alert(indicators.length),并非我们期望的 alert(i)。
也就是onclick事件触发时候, i的值是循环后最后的值。(ps: 通过闭包以及作用域我们知道事件触发时候i当时的值是最后的值)。

FAQ : 那怎么去锁定值i并且有效的保存当时的状态. 通过引用一个立即调用函数表达式即可。

Answer: 使用IIFE。

{% highlight js %}
var indicators = document.getElementsByTagName("li");
for (var i = 0; i < indicators.length; i++) {
	 (function(temp){
		 indicators[i].onclick = function() {
		 	alert(temp);
		 }
	})(i); /*! 通过IIFE来锁定值i并且有效的保存当时的状态 */
}
{% endhighlight %}

当你点击指示器的时候,执行结果就是你点击对应的指示器index。

### let关键字
   * let解释
   
	   > let 允许把变量的作用域限制在块级域中。与 var 不同处是：var 声明变量要么是全局的，要么是函数级的，而无法是块级的。      
	   > let声明的变量作用域包含定义它的块以及任何包含的子块中。在这方面，let与var非常像。它们之间主要的区别在于一个var变量的作用域是整个封闭函数。
   
   
   在上个例子,我们通过IIFE来锁定值i并且有效的保存当时的状态,主要是因为var是函数级作用域的原因。接着我们看看let关键字(块作用域)如何来实现锁定值i并且有效的保存当时的状态。

{% highlight js %}
var indicators = document.getElementsByTagName("li");
/* 替换var */
for (let i = 0; i < indicators.length; i++) { 
	 var trigger = function(){
		 indicators[i].onclick = function() {
		 	alert(i);
		 }
	}
	trigger();
}

{% endhighlight %}
  
😁 是不是超级熟悉,这不就是我们很常用的语法么!

   
### End