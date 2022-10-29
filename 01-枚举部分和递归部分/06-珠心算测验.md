# ACWing459 珠心算测验

### 问题描述

题目链接：[459. 螺旋矩阵 - AcWing题库](https://www.acwing.com/problem/content/461/)

一个 nn 行 nn 列的螺旋矩阵可由如下方法生成： 

从矩阵的左上角（第 11 行第 11 列）出发，初始时向右移动；如果前方是未曾经过的格子，则继续前进，否则右转；重复上述操作直至经过矩阵中所有格子。

根据经过顺序，在格子中依次填入 1,2,3,…,n21,2,3,…,n2，便构成了一个螺旋矩阵。

下图是一个 n=4n=4 时的螺旋矩阵。

![QQ截图20190311074436.png](https://www.acwing.com/media/article/image/2019/03/11/19_7b33d6cc43-QQ%E6%88%AA%E5%9B%BE20190311074436.png)

现给出矩阵大小 nn 以及 ii 和 jj，请你求出该矩阵中第 ii 行第 jj 列的数是多少。

#### 输入格式

输入共一行，包含三个整数 n，i，jn，i，j，每两个整数之间用一个空格隔开，分别表示矩阵大小、待求的数所在的行号和列号。

#### 输出格式

输出共一行，包含一个整数，表示相应矩阵中第 ii 行第 jj 列的数。

#### 数据范围

1≤n≤300001≤n≤30000,
1≤i,j≤n1≤i,j≤n

#### 输入样例：

```
4 2 3
```

#### 输出样例：

```
14
```

### 算法思路

当然可以按照题目给定的方式螺旋一周，获得所有格子的数据，但是这样做的话会耗费大量的时间。本题只要获取求一个格子的值，那我们先来看看这种螺旋方式有什么规律吧。

事实上，可以把一次螺旋的右、下、左、上视为一个周期，如本题给出的例子中的1-12是一个周期，13-16也是一个周期，而每个周期有多少个元素是容易求得的，那么只要求出我们要找的元素是第几个周期的第几个元素就可以获取它的值是多少了。从这个意义上来说，本题是运用了数学知识来简化计算难度的。本题里面的findposition还可以简化，因为知道了它所在拿个周期上就可以直接计算出它的位置了：如果是在顶部，那就是j，右部就是N-T+i，以此类推，我这里偷个小懒，就不实现了。

### 实现代码

```java
package Enum;

import java.util.Scanner;
import java.lang.Math;
public class SpiralMaritx {
	
	static int N;
	static int i,j;
	public static int getNums(int T)   {
		if(N+1 - 2*T == 0) return 1;
		return 4*(N+1-2*T);
	}
	public static int findPosition(int T) {
		int position = 0;
		for(int k =T;k<=N-T+1;k++) {
			position++;
			if(i==T&&j==k) return position;
		}
		for(int k =T+1;k<=N-T+1;k++) {
			position++;
			if(j==N-T+1&&i==k) return position;
		}
		for(int k = N-T;k>=T;k--) {
			position++;
			if(i==N-T+1&&j==k) return position;
		}
		for(int k = N-T;k>=T+1;k--) {
			position++;
			if(i==k&&j==T) return position;
		}
		return 0;
	}
	public static void main(String args[]) {
		Scanner sc = new Scanner(System.in);
		N = sc.nextInt();

		i=sc.nextInt();
		j=sc.nextInt();
		int T,position = 0;
		//求出i,j是在第几个周期的第几个数
		T=Math.min(i, j);
		T=Math.min(T, N+1-j);
		T=Math.min(T, N+1-i);
		position = findPosition(T);
		//根据它所在的周期和周期内位置获取它的值
		for(i=1;i<T;i++) {
			position += getNums(i);
		}
		System.out.println(position);
	}
}

```

