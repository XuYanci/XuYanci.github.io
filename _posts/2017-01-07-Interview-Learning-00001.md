---
layout: post
title: "Interview-Learning-00001"
date: 2016-12-22
excerpt: "Interview-Learning-00001"
tags: [Interview 2017]
comments: true
---

  今天在群里看到有同学发了两道华为的面试题目，就看了一下如何解决。


*  编写一个程序,要求计算从1+2+3+...100
每加一次输出一次结果.
程序不得超过20行
在程序中不得出现for,while关键字。

		/*! 递归函数,用于计算1+2+3+... */
		- (NSUInteger)dg:(NSUInteger)i {
		    if (i == 0) {
		        return 0;
		    }
		    i +=  [self dg:i - 1];
		    NSLog(@"%ld",i);
		    return i;
		}
		
		/*! 执行函数 */
		[self dg:100]




*  一个人买汽水, 1块钱一瓶汽水,2个空瓶可以换一瓶汽水,3个瓶盖可以换一瓶汽水.这个人有20块钱,问:总共能喝多少汽水.



#####按照题意 逐步推导一下:

定义 M = 一块钱， W = 一瓶水，PG = 一个瓶盖 ， KP = 一个空瓶。

---

1M = 1W

Left:

1PG + 1KP

---

2M = 4W

LEFT

1KP

---

3M = 6W

LEFT

1KP + 2PG

---

4M = 11W

LEFT

1KP

---

5M = 13W

LEFT 

1KP + 2PG

---



	2| 4 
	
	3| 4 + 2 
	
	4| 4 + 2 + 5
	
	5| 4 + 2 + 5 + 2
	
	6| 4 + 2 + 5 + 2 + 5

---

推导一下,得出规律:

偶数 + 5

奇数 + 2

---

ADT:
		
		int total = 4; /*! 2元钱开始计算 */
		for (int i = 3; i <= 20; i++) {
			if (i % 2 == 0) 
				total += 5;
			else 
				total += 2;
		}
		

PS : 若使用程序来解决,原来是这样。

	int money = 20; // 金额
	int emptyBottle = 0; // 空瓶 
	int BottleCap = 0; // 瓶盖
	int mineral = 0;  // 瓶数
	for (int i = 0; i < money;i++) {
		mineral ++；
		emptyBottle++;
		BottleCap++;
		if(emptyBottle == 2) {
			mineral ++;
			emptyBottle = 0;
		}
		if(BottleCap == 3) {
			mineral ++;
			BottleCap = 0;
		}
	}
	
	System.out.println(mineral);

		


