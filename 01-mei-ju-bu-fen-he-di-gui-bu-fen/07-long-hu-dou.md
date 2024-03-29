# ACWing474 龙虎斗

### 题目描述

题目链接：[474. 龙虎斗 - AcWing题库](https://www.acwing.com/problem/content/476/)

轩轩和凯凯正在玩一款叫《龙虎斗》的游戏，游戏的棋盘是一条线段，线段上有 n𝑛 个兵营（自左至右编号 1∼n1∼𝑛），相邻编号的兵营之间相隔 11 厘米，即棋盘为长度为 n−1𝑛−1 厘米的线段，i𝑖 号兵营里有 cic𝑖 位工兵。

下面图为 n=6𝑛=6 的示例：

![QQ截图20190312051117.png](https://www.acwing.com/media/article/image/2019/03/12/19_422a57b644-QQ%E6%88%AA%E5%9B%BE20190312051117.png)

轩轩在左侧，代表“龙”；凯凯在右侧，代表“虎”。 

他们以 mm 号兵营作为分界，靠左的工兵属于龙势力，靠右的工兵属于虎势力，而第 mm 号兵营中的工兵很纠结，他们不属于任何一方。  

一个兵营的气势为：该兵营中的工兵数 ×× 该兵营到 mm 号兵营的距离；参与游戏一方的势力定义为：属于这一方所有兵营的气势之和。

下面图为 n=6,m=4n=6,𝑚=4 的示例，其中红色为龙方，黄色为虎方：

![QQ截图20190312051128.png](https://www.acwing.com/media/article/image/2019/03/12/19_689b6eb244-QQ%E6%88%AA%E5%9B%BE20190312051128.png)

游戏过程中，某一刻天降神兵，共有 s1𝑠1 位工兵突然出现在了 p1𝑝1 号兵营。

作为轩轩和凯凯的朋友，你知道如果龙虎双方气势差距太悬殊，轩轩和凯凯就不愿意继续玩下去了。

为了让游戏继续，你需要选择一个兵营 p2𝑝2，并将你手里的 s2𝑠2 位工兵全部派往兵营 p2𝑝2，使得双方气势差距尽可能小。  

注意：你手中的工兵落在哪个兵营，就和该兵营中其他工兵有相同的势力归属（如果落在 mm 号兵营，则不属于任何势力）。

#### 输入格式

输入文件的第一行包含一个正整数 n𝑛，代表兵营的数量。  

接下来的一行包含 n𝑛 个正整数，相邻两数之间以一个空格分隔，第 i𝑖 个正整数代表编号为 i𝑖 的兵营中起始时的工兵数量 ci𝑐𝑖。  

接下来的一行包含四个正整数，相邻两数间以一个空格分隔，分别代表 m,p1,s1,s2𝑚,𝑝1,𝑠1,𝑠2。

#### 输出格式

输出文件有一行，包含一个正整数，即 p2𝑝2，表示你选择的兵营编号。

如果存在多个编号同时满足最优，取最小的编号。

#### 数据范围

1<m<n1<m<n,
1≤p1≤n1≤p1≤n,
n≤105,ci,s1,s2≤109n≤105,ci,s1,s2≤109

#### 输入样例：

```
6 
2 3 2 3 2 3
4 6 5 2
```

#### 输出样例：

```
2
```

### 算法思路

额，不太清楚这个题意义何在，除了输入和计算双方势力差需要O(n)的时间，剩下直接通过小学数学知识在O(1)的时间内就可以得到解了。

### 实现代码

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.StreamTokenizer;
import java.lang.Math;

public class Main {
	public static long N;
	public static StreamTokenizer in;
	public static long c[];
	public static long s1,s2;
	static int p1,m;
	public static long p2;
	public static long  m1,m2;
	public static void main(String args[]) throws IOException{
		in = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));
		in.nextToken();
		N=(long)in.nval;
		c= new long[(int) (N+1)];
		for(int i=1;i<=N;i++) {
			in.nextToken();
			c[i]=(long)in.nval;
		}
		in.nextToken();
		m=(int)in.nval;
		in.nextToken();
		p1=(int)in.nval;
		in.nextToken();
		s1=(long)in.nval;
		in.nextToken();
		s2=(long)in.nval;
		m1=0;m2=0;
		c[p1]+=s1;
		for(int i=1;i<m;i++)
			m1+=(m-i)*c[i];
		for(int i=1+m;i<=N;i++)
			m2+=(i-m)*c[i];
		if(m1>m2) {
			long d=m1-m2;
			if(Math.abs(d-d/s2*s2)>Math.abs(d-(d/s2+1)*s2))
				p2=m+d/s2+1;
			else p2=m+d/s2;
		}
		else if(m1<m2) {
			long d=m2-m1;
			if(Math.abs(d-d/s2*s2)>=Math.abs(d-(d/s2+1)*s2))
				p2=m-d/s2-1;
			else p2=m-d/s2;
		}
		else p2=m;
		System.out.println(p2);
	}
}

```

