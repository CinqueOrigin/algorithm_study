# ACWing443 导弹拦截

### 问题描述

问题链接：[443. 导弹拦截 - AcWing题库](https://www.acwing.com/problem/content/description/445/)

经过 1111 年的韬光养晦，某国研发出了一种新的导弹拦截系统，凡是与它的距离不超过其工作半径的导弹都能够被它成功拦截。

当工作半径为 00 时，则能够拦截与它位置恰好相同的导弹。

但该导弹拦截系统也存在这样的缺陷：每套系统每天只能设定一次工作半径。

而当天的使用代价，就是所有系统工作半径的平方和。 

某天，雷达捕捉到敌国的导弹来袭。

由于该系统尚处于试验阶段，所以只有两套系统投入工作。

如果现在的要求是拦截所有的导弹，请计算这一天的最小使用代价。

#### 输入格式

第一行包含 44 个整数 x1、y1、x2、y2x1、y1、x2、y2，每两个整数之间用一个空格隔开，表示这两套导弹拦截系统的坐标分别为 (x1,y1)、(x2,y2)(x1,y1)、(x2,y2)。  

第二行包含 11 个整数 NN，表示有 NN 颗导弹。

接下来 NN 行，每行两个整数 x、yx、y，中间用一个空格隔开，表示一颗导弹的坐标 (x,y)(x,y)，不同导弹的坐标可能相同。

#### 输出格式

只有一行，包含一个整数，即当天的最小使用代价。

#### 数据范围

1≤N≤1051≤N≤105,所有坐标分量的绝对值都不超过 10001000。

#### 输入样例：

```
0 0 6 0
5
-4 -2
-2 3
4 0
6 -2
9 1
```

#### 输出样例：

```
30
```

### 实现思路
	本题其实用简单的枚举就可以做出来，本题有两个导弹发射系统，对于第一套系统，它可取的半径实际上最多有N+1个(N个不同的导弹对应N个不同的半径，还有一个可以取0)，每一种半径都能包含一定的导弹，剩下的导弹交给另一套导弹系统即可，所以只要枚举这N+1种情况，在实际操作的过程中，首先要按照这些导弹离第一套系统的距离进行排序，否则每种情况都要循环判断哪些导弹在射程之内，会增加时间复杂度。

### 实现代码
```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;
import java.util.Arrays;
import java.lang.Math;

public class Main {
	static int x1,y1,x2,y2;
	static int N;
	static StreamTokenizer in;
	static PrintWriter out;
	static class point implements Comparable<point>{
		public int d1,d2;//这里的两个属性是到两套导弹系统的距离，而不是它的坐标，这样可以节省资源
		
		public point(int d1,int d2) {
			this.d1=d1;
			this.d2=d2;
		}
		public int compareTo(point p) {
			return p.d1-d1;
		}
		}
	public static void main(String args[]) throws IOException {
		in = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));
		out = new PrintWriter(new BufferedWriter(new OutputStreamWriter(System.out)));
		in.nextToken();
		x1=(int)in.nval;
		in.nextToken();
		y1=(int)in.nval;
		in.nextToken();
		x2=(int)in.nval;
		in.nextToken();
		y2=(int)in.nval;
		in.nextToken();
		N=(int)in.nval;
		point points[]=new point[N];
		//获取输入的导弹数据，并处理为距离
		for(int i = 0;i<N;i++) {
			in.nextToken();
			int x=(int)in.nval;
			in.nextToken();
			int y=(int)in.nval;
			points[i]=new point((x-x1)*(x-x1)+(y-y1)*(y-y1), (x-x2)*(x-x2)+(y-y2)*(y-y2));
		}
		Arrays.sort(points);
		int cos=0;
		int d1,d2=0;
		d1=points[0].d1;
		d2=0;
		int result=d1;
		for(int i =1;i<N;i++) {
		d1=points[i].d1;
		d2=Math.max(points[i-1].d2,d2);
		result=Math.min(result, d1+d2);
		}
		out.println(result);
		out.flush();
	}
}

```