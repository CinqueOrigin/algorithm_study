# Acwing1534 递归实现排列类型枚举 2

### 问题描述

[问题链接](https://www.acwing.com/problem/content/1539/)

给定一个长度为 nn 的可包含重复数字的序列，请你求出其所有不重复的全排列。

#### 输入格式

第一行包含整数 nn。

第二行包含 nn 个整数。

#### 输出格式

输出所有的不同排列，每种排列占一行。

在确定每种排列的输出顺序时，第一个数较小的先输出，第一个数相同时，第二个数较小的先输出，以此类推。

#### 数据范围

1≤n≤91≤n≤9,
数组中包含的元素的取值范围 [1,9][1,9]

#### 输入样例：

```
3
1 1 2
```

#### 输出样例：

```
1 1 2
1 2 1
2 1 1
```

### 实现思路

该题的原型是基本的回溯算法：求1-9的所有排列，不同之处在于本题给出的元素是允许重复的。如何去掉重复元素呢？去掉重复元素的关键是把所有的同一种元素视为一个元素，那么如何保证这三个数的排列是不会重复的呢？只要保证这些相同的数不要互相交换顺序就好了。首先要明确原来的算法是哪一步可能导致顺序的交换，答案是DFS(i)的每层循环中设置第i位的数字的时候，例如输入为1213，那么在dfs（0）的第三次循环就是一次交换顺序。那么如何来避免顺序交换呢？只要保证在交换顺序的那次循环中能够发现之前使用过这个数就可以了。这个的实现方法有许多种，比较容易想到的就是在获取输入数据的时候另外设置一个数组，这些数组标明该数上一次出现的位置，如果在某次循环中要把一个数设置的位置小于等于数组中记录的位置，那么就跳过这次循环。一种比较巧的方法是，首先给获取的输入的数据排个序，这样就可以保证所有的重复数字都是相邻的，那么只要某次循环中这个数字的下一个数字和它相等，那么就跳过这次循环就好了。这个想法是没问题的，可惜超时了，同样的方法放在c++上很快就完成了，经过一番探讨后，发现是输出的地方出了问题。
使用System.out.print这种输出花了6秒，使用带缓冲的PrintWriter只用了277ms，这差距还是蛮大的，为什么差别这么大呢因为System.out.println这种方法是自带flush功能的.进而考虑到当输入量大时，也应该更换输入方法，这里推荐使用StreamTokenizer。

#### PrintWriter
使用方法如下：
```java
PrintWriter out = new PrintWriter(new BufferedWriter(new OutputStreamWriter(System.out)));
out.print(Temp[j]+" ");
 out.println();
 out.flush();   //需要刷新屏幕
```

#### StreamTokenizer
使用方法如下：
```java
StreamTokenizer in = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));//定义一个StreamTokenizer
        for(int i=0;i<N;i++){
            in.nextToken();         //切换到下一个
            Num[i]=(int)(in.nval);  //读取切换后的值
        }
```
### 实现代码1

```java
import java.io.*;
import java.util.Arrays;
import java.util.Scanner;

public class Main {
    static int N;
    static int[] Num;
    static int[] Temp;
    static boolean[] state;
    static PrintWriter out;
    public static void DFS(int i){
        if(i == N){
            for(int j =0;j<N;j++){
                out.print(Temp[j]+" ");
            }
            out.println();
            return;
        }
        for (int j =0;j<N;j++){
            if (!state[j]){
                Temp[i]=Num[j];
                state[j]=true;
                DFS(i+1);
                state[j]=false;
                while(j+1<N&&Num[j]==Num[j+1])j++;
            }
        }
    }
    public static void main(String[] args) throws IOException {
        StreamTokenizer in = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));
        in.nextToken();
        N=(int)(in.nval);
        out= new PrintWriter(new BufferedWriter(new OutputStreamWriter(System.out)));
        Num=new int[N];
        Temp=new int[N];
        state = new boolean[N];
        for(int i=0;i<N;i++){
            in.nextToken();
            Num[i]=(int)(in.nval);
        }
        long startTime = System.nanoTime();
        Arrays.sort(Num);
        DFS(0);
        long endTime = System.nanoTime();
//        out.println("time:"+(endTime-startTime)/1000000);
        out.flush();
    }
}
```

