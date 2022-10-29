# ACWing126 最大的和

### 问题描述

问题的链接：[126. 最大的和 - AcWing题库](https://www.acwing.com/problem/content/128/)

给定一个包含整数的二维矩阵，子矩形是位于整个阵列内的任何大小为 1×11×1 或更大的连续子阵列。

矩形的总和是该矩形中所有元素的总和。

在这个问题中，具有最大和的子矩形被称为最大子矩形。

例如，下列数组：

```
0 -2 -7 0 
9 2 -6 2 
-4 1 -4 1 
-1 8 0 -2 
```

其最大子矩形为：

```
9 2 
-4 1 
-1 8 
```

它拥有最大和 1515。

#### 输入格式

输入中将包含一个 N×NN×N 的整数数组。

第一行只输入一个整数 NN，表示方形二维数组的大小。

从第二行开始，输入由空格和换行符隔开的 N2N2 个整数，它们即为二维数组中的 N2N2 个元素，输入顺序从二维数组的第一行开始向下逐行输入，同一行数据从左向右逐个输入。

数组中的数字会保持在 [−127,127][−127,127] 的范围内。

#### 输出格式

输出一个整数，代表最大子矩形的总和。

#### 数据范围

1≤N≤1001≤N≤100

#### 输入样例：

```
4
0 -2 -7 0 9 2 -6 2
-4 1 -4  1 -1

8  0 -2
```

#### 输出样例：

```
15
```

### 实现思路

我们当然可以进行暴力枚举，枚举$n^2$个点以及O($n^2$)个大小，总共的时间复杂度是O($n^4$)这个题目的数据有点不强，依然可以过。但是通过动态规划的方法可以进行优化。
优化1：参考一维数组的最大子序列的求法，假设我们用sum(i)表示0-i这几个点的总和，那么$f(i)＝a【i】$(如果f(i-1)>0);否则f(i)=a【i】+f(i-1);
类似地，我们可以给定一个子矩阵的上边界、下边界，然后从左到右动态规划，只不过和一维数组不同的是这里的数组的行数不一定是只有一行，它取决于上边界、下边界的值，这样我们就可以循环遍历上下边界，对每一个具体的上下边界进行一次这样的动态规划，这种算法只需要o($n^3$);需要注意的是，因为这里需要提取的一维数组是“多行”的，如果是将输入保存到一个数组中，那么每次获取多行的值是需要进行一次循环计算的，这里建议在输入的时候直接把输入数据转化为sum，也就是sum【i】【j】表示第j列前i个数的和，这样的话计算会方便很多，只需要一次作差就可以求出“多行”的值了。
### 实现代码
```java
package Enum;

import java.io.*;

import static java.lang.Math.max;

public class MaxSubSum {
    static int Sum[][];
    static int N;
    static PrintWriter out;
    static StreamTokenizer in;
    public static void getMaxSubSum() {
        //1.初始化结果
        int result = -9999;
        int temp;
        //2.循环进行动态规划
        for (int i = 1; i <= N; i++){//下边界
            for(int j=1;j<=i;j++){  //上边界
                temp=0;
                for(int k=1;k<=N;k++){//对每个“一维”数组进行动态规划
                    temp=max(temp,0)+Sum[i][k]-Sum[j-1][k];
                    result=max(result,temp);
                }
            }
    }
        //3.输出结果
        out.println(result);

    }
    public static void main(String[] args) throws IOException {
        //0.初始化
        out=new PrintWriter(new BufferedWriter(new OutputStreamWriter(System.out)));
        in = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));
        //1.获取输入
        in.nextToken();
        N=(int)in.nval;
        Sum=new int[N+1][N+1];
        for(int i=1;i<=N;i++) Sum[0][i]=0;
        for(int i = 1;i<=N;i++){
            for(int j=1;j<=N;j++)
            {
                in.nextToken();
                Sum[i][j]=(int)in.nval;
                Sum[i][j]+=Sum[i-1][j];
            }
        }
        //2.调用函数
        getMaxSubSum();
        //3.flush
        out.flush();
    }
}

```